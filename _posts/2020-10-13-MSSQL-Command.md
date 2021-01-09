---
title: MSSQL SQL 명령어 (MSSQL SQL Command)
author: Sangjun Cha
date: 2020-10-13 14:52:25 +0900
categories: [DB, MSSQL]
tags: [db, mssql, sql]
pin: false
---

# 1. 테이블 생성 (Create Table)

## 1.1 테이블 생성 문법 구조

    ```sql
    CREATE TABLE 테이블명(
    		컬럼명 타입(크기) NOT NULL, --NULL 값이 들어갈 수 없음
    		컬럼명 타입 NULL DEFAULT(값), --초기값 지정
    		CONSTRAIN PK이름 PRIMARY KEY(컬럼명) --PK설정
    )
    ```

## 1.2 테이블 생성 (예제)

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
    CREATE TABLE TB_AIX_AI001I(
    		ID int PRIMARY KEY,
    		AI_JSON nvarchar NULL,
    		BL nvarchar(11) NOT NULL,
    		IMGE_PATH nvarchar(300) NOT NULL, 
    		IMGE_NAME nvarchar(100) NOT NULL, 
    		DRCT_VH nvarchar(2) NOT NULL, 
    		IN_DT datetime default(GETDATE()) NOT NULL
    )
    ```

# 2. 테이블 수정 (Alter Table)

## 2.1 테이블 컬럼 확인 (구조)

    ```sql
    SP_COLUMNS {테이블 이름}

    SP_HELP {테이블 이름}
    ```

    ```sql
    SP_COLUMNS MY_TABLE

    SP_HELP MY_TABLE
    ```

## 2.2 테이블 변경 (칼럼 추가)

    ```sql
    ALTER TABLE 테이블명 ADD 컬럼명 컬럼 속성
    ```

    ```sql
    ALTER TABLE MY_TABLE ADD NM_ENG NVARCHAR NOT NULL
    ```

## 2.3 테이블 변경 (칼럼 수정)

    ```sql
    ALTER TABLE 테이블명 ALTER 컬럼명 컬럼 속성
    ```

    ```sql
    ALTER TABLE MY_TABLE ALTER COLUMN NM_ENG INT
    ```

## 2.4 테이블 변경 (칼럼 삭제)

    ```sql
    ALTER TABLE 테이블명 DROP COLUMN 칼럼명
    ```

    ```sql
    ALTER TABLE MY_TABLE DROP COLUMN NM_ENG
    ```

# 3. 테이블 삭제 (Drop Table)

## 3.1 테이블 삭제

    ```sql
    DROP TABLE 테이블명
    ```

    ```sql
    DROP TABLE MY_TABLE
    ```

# 4. json 데이터

## 4.1 json 데이터 삽입 (insert)

- 사용 예제

    ```sql
    INSERT INTO {table_name} VALUES (1, N'{"EmployeeInfo": {
                "FirstName": "John",
                "LastName": "Doe",
                "Dob": "12-Jan-1970",
                "AnnualSalary": 85000
            }}')
    ```

## 4.2 json 특정 데이터 호출 (JSON_VALUE)

같은 속성이 여러개 있다면 인덱스로 접근 가능. 인덱스는 0부터 시작

- 사용 예제

    ```sql
    SELECT JSON_VALUE(json_data, '$.EmployeeInfo.FirstName') FROM jsontest
    ```

## 4.3 JSON 형식으로 내보내기 (FOR JSON)

for json은 테이블에 있는 데이터들을 JSON 형식으로 내보내기 위한 함수

- 사용 예제

    ```sql
    SELECT * FROM jsontest FOR JSON AUTO
    ```

## 4.4 json 필드 생성 예제

- 사용 예제

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

    ```sql
    INSERT INTO dbo.TB_JSONTEST (ID, DATA_JSON, SEQ, IMAGE_PATH, IMAGE_NAME, DIRECT_CODE)
    VALUES (2, N'{"EmployeeInfo": {"FirstName": "John", "LastName": "Doe", "Dob": "12-Jan-1970", "AnnualSalary": 85000}}', 'A12345', 'D:\image\', 'D:\image\image001.jpg', 'V')
    ```

    ```sql
    select * from TB_JSONTEST
    ```

    ```sql
    DROP TABLE TB_JSONTEST
    ```