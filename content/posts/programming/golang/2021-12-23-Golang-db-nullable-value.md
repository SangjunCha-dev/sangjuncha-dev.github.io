---
title: "golang db nullable 데이터 처리"
date: 2021-12-23T16:39:22+09:00
description: golang db nullable 데이터 처리
tags: ["go", "db"]
categories: ["Go", "db"]
---


---

golang에서는 타입별로 정해진 `zero value`가 있는데, DB colume 타입의 `zero value`와 맞지 않을 때 다음과 에러가 발생한다.

```
panic: sql: Scan error on column index : converting NULL to string is unsupported
```

각 타입별 zero value

- 문자열 타입 string : ""
- 부울린 타입 boolean : false
- 정수형, Float등 숫자형 타입 : 0
- 기타 타입 : nil


---

## 1. 사전환경

아래의 글을 진행했다는 가정에서 설명한다.

[golang postgreDB CURD](https://sangjuncha-dev.github.io/posts/programming/golang/2021-12-16-golang-postgredb-crud/)


---

## 2. 에러가 발생한 코드

기존에 작성한 코드에서 Update 함수의 `name` 부분을 `nil` 값으로 수정하여 실행한다.

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
		err := rows.Scan(&id, &name, &age)  // name 타입 에러 발생
		if err != nil {
			panic(err)
		}

		fmt.Printf("[Read] id: %d, name: %v, age: %d\n", id, name, age)
	}
}

func Update(id int64, user User) {
	sqlStatement := `UPDATE user_test SET name=$2, age=$3 WHERE id=$1`

	res, err := db.Exec(sqlStatement, id, nil, user.Age)
	if err != nil {
		panic(err)
	}

	rowsAffected, err := res.RowsAffected()
	if err != nil {
		panic(err)
	}

	fmt.Printf("[Update] Total rows/record affected %v\n", rowsAffected)
}

func main() {
	InitDB()

    Create("foo", 10)
    Update(1, User{Age: 20})
	Read(1)

	defer db.Close()
}
```

데이터 수정후 해당 데이터를 조회하면 아래와 같은 에러가 발생한다. 

golang에서는 타입별로 정해진 `zero value`와, DB colume 타입의 `zero value`와 맞지 않아서 발생한 에러이다.

```bash
> go run main.go
[Update] Total rows/record affected 1
panic: sql: Scan error on column index 1, name "name": converting NULL to string is unsupported
```


---

## 3. 문제 해결한 코드

`database/sql` 패키지의 Null*자료형을 사용한다.  
`Null* 자료형`은 zerovalue가 `null`값이 아닌 기본 자료형을 지원한다.

- `sql.NullString` : null 유무가 포함된 문자열 타입
- `sql.NullString.Valid`: null값이면 false, 아니면 true 값을 가진다.
- `sql.NullString.String`: 해당 변수의 문자열 값이다.

```go
func Read(userId int) {
	sqlStatement := `SELECT "id", "name", "age" FROM "user_test" WHERE $1 = "id"`
	rows, err := db.Query(sqlStatement, userId)
	if err != nil {
		panic(err)
	}
	defer rows.Close()

	var id int
	var name sql.NullString
	var age int
	for rows.Next() {
		err := rows.Scan(&id, &name, &age)
		if err != nil {
			panic(err)
		}

		if name.Valid {
			fmt.Printf("[Read] id: %d, name: %v, age: %d\n", id, name.String, age)
		} else {
			fmt.Printf("[Read] id: %d, name: %v, age: %d\n", id, name, age)
		}
	}
}

func main() {
	InitDB()

	Read(1)
	Read(2)

	defer db.Close()
}
```

`.Valid` 조건문을 통해 `nil`값에 대한 필터링을 하거나 별도로 처리할 수 있다.

```bash
> go run main.go
[Read] id: 1, name: { false}, age: 20
[Read] id: 2, name: bar, age: 20
```


---

## 4. 예시코드 Git

[golang-db-nullable-value](https://github.com/SangjunCha-dev/blog/tree/main/golang/golang-db-nullable-value)
