---
title: "golang postgreDB CURD"
date: 2021-12-16T13:09:17+09:00
description: golang postgreDB CURD
tags: ["go", "postgredb"]
categories: ["Go", "PostgreDB"]
---


---

golang에서 postgre데이터 베이스의 SQL 생성, 수정, 읽기, 삭제 기능의 간단한 사용법이다.

postgreDB가 이미 설치되어 있다는 전제하에 진행한다.

- 로컬 설정 : [PostgreDB 설치](https://sangjuncha-dev.github.io/posts/db/2020-09-29-postgredb-setup/) 
- 도커 이미지 : [PostgreDB, pgadmin4 도커 설치 방법](https://sangjuncha-dev.github.io/posts/docker/2021-04-29-docker-postgresdb-pgadmin4-setup-guide/)


---

## 1. 라이브러리 설치

go version : 1.17

```
> go get github.com/lib/pq
```


---

## 2. 테이블 구조

postgreDB에 아래와 같은 구조의 User 테이블이 선언된 상태로 진행한다.

```sql
CREATE TABLE user (
   id SERIAL PRIMARY KEY,
   name VARCHAR(20),
   age INT
);
```


---

## 3. DB 설정 초기화

```go
package main

import (
	"database/sql"
	"fmt"

	_ "github.com/lib/pq"
)

// User 스키마 또는 User 테이블구조
type User struct {
	Id   int
	Name string
	Age  int
}

var (
	db *sql.DB
)

// DB 설정
func InitDB() {
	host := "localhost"
	port := 5432
	name := "TEST"
	user := "test_onwer"
	password := "1234"

	psqlconn := fmt.Sprintf("host=%s port=%d dbname=%s user=%s password=%s sslmode=disable",
		host, port, name, user, password)

	var err error
	db, err = sql.Open("postgres", psqlconn)
	if err != nil {
		panic(err)
	}
}

// DB 연결테스트
func ConnectTest() {
	err := db.Ping()
	if err != nil {
		panic(err)
	}
}

func main() {
	InitDB()

	ConnectTest()

	defer db.Close()
}
```


---

## 4. create

데이터 생성

- `Prepare` : 다중쿼리나 실행을 위한 구문을 생성
- `QueryRow` : 준비된 쿼리 구문을 입력받은 인자로 실행
- `Scan` : 데이터베이스로 반환받은 columns 읽기 

```go
func Create(name string, age int) {
	sqlStatement := `INSERT INTO user_test (name, age) VALUES ($1, $2) returning id`
	stmt, err := db.Prepare(sqlStatement)

	var id int64
	err = stmt.QueryRow(name, age).Scan(&id)
	if err != nil {
		panic(err)
	}
	fmt.Println("create id :", id)
}

func main() {
	InitDB()

	Create("foo", 10)
	Create("bar", 20)

	defer db.Close()
}
```

create 결과

```bash
create id : 1
create id : 2
```


---

## 5. read

데이터 조회

```go
func Read(userId int) {
	sqlStatement := `SELECT "id", "name", "age" FROM "user_test" WHERE $1 = "id"`
	rows, err := db.Query(sqlStatement, userId)
	if err != nil {
		panic(err)
	}
	defer rows.Close()

	var id int
	var name string
	var age int
	for rows.Next() {
		err := rows.Scan(&id, &name, &age)
		if err != nil {
			panic(err)
		}

		fmt.Printf("id: %d, name: %v, age: %d\n", id, name, age)
	}
}

func main() {
	InitDB()

	Read(1)

	defer db.Close()
}
```

read 결과

```bash
id: 1, name: foo, age: 10
```


---

## 6. update

데이터 수정

```go
func Update(id int64, user User) {
	sqlStatement := `UPDATE user SET name=$2, age=$3 WHERE id=$1`

	res, err := db.Exec(sqlStatement, id, user.Name, user.Age)
	if err != nil {
		panic(err)
	}

	rowsAffected, err := res.RowsAffected()
	if err != nil {
		panic(err)
	}

	fmt.Printf("Total rows/record affected %v\n", rowsAffected)
}

func main() {
	InitDB()

	Update(1, User{Name: "foo1", Age: 21})
	Read(1)

	defer db.Close()
}
```

update 결과

```bash
Total rows/record affected 1
id: 1, name: foo1, age: 21
```


---

## 7. delete

데이터 삭제

```go
func Delete(id int64) {
	sqlStatement := `DELETE FROM user WHERE id=$1`

	res, err := db.Exec(sqlStatement, id)
	if err != nil {
		panic(err)
	}

	rowsAffected, err := res.RowsAffected()
	if err != nil {
		fmt.Println("Error while checking the affected rows.", err)
	} else {
		fmt.Println("Total rows/record affected", rowsAffected)
	}
}

func main() {
	InitDB()

	Delete(2)

	defer db.Close()
}
```

delete 결과

```bash
Total rows/record affected 1
```


---

## 8. 예시코드 Git

[golang-postgredb-crud](https://github.com/SangjunCha-dev/blog/tree/main/golang/golang-postgredb-crud)


---

## 참고(Reference)

- [GoLang PostgreSQL Example](https://golangdocs.com/golang-postgresql-example)
