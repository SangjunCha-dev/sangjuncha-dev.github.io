---
title: PostgreDB SQL 명령어 (PostgreDB SQL Command)
author: Sangjun Cha
date: 2020-10-20 15:27:33 +0900
categories: [DB, PostgreDB]
tags: [db, postgredb, sql]
pin: false
---



참고 - PostgreDB의 경우 대문자인식은 ""으로만 가능

# 1. DATABASE 생성

- DATABASE 생성

    ```sql
    CREATE DATABASE "{database_name}";
    ```


# 2. USER 생성

- USER 생성

    ```sql
    CREATE USER {user_name} WITH PASSWORD '{user_password}'
    ```

- 개발용 계정으로 사용할 예정이기 때문에 SUPAERUSER 권한부여

    ```sql
    ALTER USER {user_name} WITH SUPERUSER;
    ```

- 유저에게 특정 권한 부여

    ```sql
    GRANT {permissions} ON DATABASE {db_name} TO {user_name};
    ```

    참고 URL : [https://www.postgresql.org/docs/current/sql-grant.html](https://www.postgresql.org/docs/current/sql-grant.html)

- 예시

    ```sql
    GRANT ALL ON DATABASE "ABCD_DB" TO abcd_user;
    ```


# 3. DB insert

## 3.1 기본 insert

```sql
INSERT INTO Table_Name
VALUES (VALUE_LIST1, VALUE_LIST2, ...);
```

```sql
INSERT INTO PLAYER
VALUES ('1', 'player_name', 'code1', 1, 21);
```

## 3.2 리스트/배열 데이터 insert

```sql
INSERT INTO Table_Name
VALUES ('{LIST_VALUE}');
```

```sql
INSERT INTO "WeightsFile" 
VALUES (1, 'test_Weights.npz', '{test_catagory1, test_catagory2}', 1, 'color')
```

## 3.3 2차원 리스트 저장방법

사용환경 : django, postgreDB

키를 index로 값을 data로 변환

```python
# models.py
class modelName(models.Model):
    columnName = models.JSONField()

# process.py (2차원 리스트 > json)
from .models import modelName

Dict = {}
for i in range(len(list1)):
    Dict[i] = list2['data'][i]

modelInstance = modelName(
    columnName = Dict
)
modelInstance.save()

# process.py (json > 2차원 리스트)
exportList = list(modelName.objects.filter(id=id).values('id', 'columnName'))

for i in range(len(exportList)):
    exportList[i]['data'] = list(exportList[i]['data'].values())
```


# 4. 기본키 초기화

```sql
ALTER SEQUENCE "TableName_id_seq" RESTART WITH 1;
```

## 4.1 delete 테이블 데이터 삭제 및 ID값 초기화 예제

```sql
delete from "UploadData" where id > 0;
ALTER SEQUENCE "UploadData_id_seq" RESTART WITH 1;

delete from "Annotations" where id > 0;
ALTER SEQUENCE "Annotations_id_seq" RESTART WITH 1;

delete from "Categories" where id > 0;
ALTER SEQUENCE "Categories_categories_id_seq" RESTART WITH 1;

delete from "Images" where id > 0;
ALTER SEQUENCE "Images_id_seq" RESTART WITH 1;
```
