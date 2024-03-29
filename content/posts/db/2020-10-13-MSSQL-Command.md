---
title: "MSSQL SQL 명령어 (MSSQL SQL Command)"
date: 2020-10-13T14:52:25+09:00
description: MariaDB SQL 명령어 예시
# menu:
#   sidebar:
#     name: mssql-sql-command
#     identifier: mssql-sql-command
#     parent: db
#     weight: 30
tags: ["db", "mssql", "sql"]
categories: ["DB", "MSSQL"]
---


---

## 1. 테이블 생성 (Create Table)


```sql
CREATE TABLE 테이블명(
        컬럼명 타입(크기) NOT NULL, --NULL 값이 들어갈 수 없음
        컬럼명 타입 NULL DEFAULT(값), --초기값 지정
        CONSTRAIN PK이름 PRIMARY KEY(컬럼명) --PK설정
)
```

컬럼 타입 : INT / NVARCHAR / VARCHAR / DATETIME 

```sql
CREATE TABLE MY_TABLE(
        NO_EMP NVARCHAR(10)NOT NULL, -- NULL 값이 들어갈 수 없음
        NM_KOR NVARCHAR(40)NOT NULL, -- NULL 값이 들어갈 수 없음
        AGE INT NULL DEFAULT (0), --초기값 = 0
        TODAY DATETIME DEFAULT(GETDATE()), --초기값 현재날짜
)
--CONSTRAINT PK_MY_TABLE PRIMARY KEY(NO_EMP)--PK : NO_EMP )
```

```sql
CREATE TABLE MY_TABLE2(
        ID int PRIMARY KEY,
        DATA_JSON nvarchar NULL,
        CODE nvarchar(20) NOT NULL,
        IMAGE_PATH nvarchar(300) NOT NULL, 
        IMAGE_NAME nvarchar(100) NOT NULL, 
        IMAGE_TYPE nvarchar(5) NOT NULL, 
        IN_DATE datetime default(GETDATE()) NOT NULL
)
```

---

## 2. 테이블 수정 (Alter Table)

### 2.1. 테이블 컬럼 확인

```sql
SP_COLUMNS {테이블명}

SP_HELP {테이블명}
```

```sql
SP_COLUMNS MY_TABLE

SP_HELP MY_TABLE
```

### 2.2. 테이블 변경 (컬럼 추가)

```sql
ALTER TABLE 테이블명 ADD 컬럼명 컬럼 속성
```

```sql
ALTER TABLE MY_TABLE ADD NM_ENG NVARCHAR NOT NULL
```

### 2.3. 테이블 변경 (컬럼 수정)

```sql
ALTER TABLE 테이블명 ALTER 컬럼명 컬럼 속성
```

```sql
ALTER TABLE MY_TABLE ALTER COLUMN NM_ENG INT
```

### 2.4. 테이블 변경 (컬럼 삭제)

```sql
ALTER TABLE 테이블명 DROP COLUMN 컬럼명
```

```sql
ALTER TABLE MY_TABLE DROP COLUMN NM_ENG
```

---

## 3. 테이블 삭제 (Drop Table)

### 3.1. 테이블 삭제

```sql
DROP TABLE 테이블명
```

```sql
DROP TABLE MY_TABLE
```

---

## 4. json 데이터

### 4.1. json 데이터 삽입 (insert)

```sql
INSERT INTO {table_name} VALUES (ID, N'{}')
```

```sql
INSERT INTO MY_TABLE VALUES (1, N'{"EmployeeInfo": {
            "FirstName": "John",
            "LastName": "Doe",
            "Dob": "12-Jan-1970",
            "AnnualSalary": 85000
        }}')
```

### 4.2. json 특정 데이터 호출 (JSON_VALUE)

같은 속성이 여러개 있다면 인덱스로 접근 가능. 인덱스는 0부터 시작

```sql
SELECT JSON_VALUE(json_data, '$.EmployeeInfo.FirstName') FROM jsontest
```

### 4.3. JSON 형식으로 내보내기 (FOR JSON)

for json은 테이블에 있는 데이터들을 JSON 형식으로 내보내기 위한 함수

```sql
SELECT * FROM jsontest FOR JSON AUTO
```

### 4.4. json 필드 생성 예제

- 테이블 생성

    ```sql
    CREATE TABLE TB_JSONTEST(
            ID int PRIMARY KEY,
            DATA_JSON nvarchar(max) NULL,
            SEQ nvarchar(11) NOT NULL,
            IMAGE_PATH nvarchar(300) NOT NULL, 
            IMAGE_NAME nvarchar(100) NOT NULL, 
            DIRECT_CODE nvarchar(2) NOT NULL, 
            IN_DATE datetime default(GETDATE()) NOT NULL
    )
    ```

- 테이블내 데이터 추가

    ```sql
    INSERT INTO dbo.TB_JSONTEST (ID, DATA_JSON, SEQ, IMAGE_PATH, IMAGE_NAME, DIRECT_CODE)
    VALUES (2, N'{"EmployeeInfo": {"FirstName": "John", "LastName": "Doe", "Dob": "12-Jan-1970", "AnnualSalary": 85000}}', 'A12345', 'D:\image\', 'D:\image\image001.jpg', 'V')
    ```

- 테이블 데이터 조회

    ```sql
    select * from TB_JSONTEST
    ```

- 테이블 삭제

    ```sql
    DROP TABLE TB_JSONTEST
    ```