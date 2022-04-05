---
title: "[MySQL][Hacker Rank] Draw The Triangle 1,2"
excerpt: "해커랭크 SQL - Alternative Queries (Easy)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, Procedure, Variable]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-27
last_motified_at:
---
<br/>
<br/>

# Problem
P(R) represents a pattern drawn by Julia in R rows. The following pattern represents P(5):

```sql
* * * * * 
* * * * 
* * * 
* * 
*
```

Write a query to print the pattern P(20).

<br/>

## 문제 설명
별 20개부터 하나씩 줄어드는 패턴을 출력하는 문제

<br/>
<br/>
<br/>

---

# Solution
별의 개수를 하나씩 줄여 출력하는 것을 20회 반복해야 한다.

반복문을 사용하는 방법과 반복문 없이 구현하는 방법 모두 살펴보자!

<br/>

## Stored Procedure
MySQL에서 반복문을 구현하기 위해서는 프로시저를 사용해야 한다.

저장 프로시저는 일련의 쿼리를 하나의 함수처럼 실행하기 위한 쿼리의 집합이다.

**사용하는 이유?**
- 하나의 요청으로 여러 SQL문을 실행할 수 있음
- 네트워크 소요 시간 단축
- 새로운 함수 선언을 통하여 직접적인 접근에서 SQL문을 보호하는 역할
- IF, CASE 그리고 LOOP 같은 제어 흐름 문장을 사용하여 보다 향상된 SQL 코드문 작성도 가능

<br/>

```sql
DELIMITER $$                  -- 프로시저 시작
CREATE PROCEDURE 프로시저명()  -- 프로시저 생성(IN|OUT 파라미터명, 타입)
	BEGIN                     -- SQL 쿼리문 시작


	END                       -- 끝
DELIMITER ;                   -- 프로시저 끝

CALL 프로시저명();  -- 저장 프로시저 호출
```
- DELIMITER $$
    - 프로시저(Procedure) 안에는 변수 설정 등 세미콜론(;) 이 여러개 등장하기 때문에 프로시저 자체를 한 명령문으로 보지 못하고, 프로시저 중간 중간에 있는 세미콜론(;) 단위로 명령문을 쪼개서 읽어버리게 됨
    - 따라서 프로시저 자체를 하나의 명령문으로 묶어주기 위해서 Delimiter 를 사용
    - 즉, 중간에 여러개의 세미콜론(;)이 있더라도 `Delimiter $$` 부터 시작해서 `Delimiter ;` 까지의 코드를 하나의 명령문으로 처리
    - `$$` 대신 `//` 도 사용 가능
- 프로시저를 생성할 때 들어올 파라미터와 나갈 파라미터를 지정할 수 있음
- `DELIMITER ;` 에서 띄어쓰기를 하지 않으면 에러 발생
<br/>

### WHILE문

```sql
WHILE (expression) DO  -- 반복할 조건
	statements           -- 실행문
END WHILE
```
<br/>
<br/>

## information_schema
- MySQL 서버 내에 존재하는 DB의 메타 정보(테이블, 칼럼, 인덱스 등의 스키마 정보)를 모아둔 DB
- 읽기 전용이며, 수정하거나 삭제할 수는 없음
- 테이블 종류
    - columns : 모든 스키마의 컬럼 확인
    - tables : 생성된 모든 테이블 종류
    - view : 생성된 뷰 정보
    - 이외에도 매우 많음

<br/>
<br/>

## Variables
**사용자 정의 변수 (User-Defined Variables)**
    - @로 시작
    - 한 클라이언트에서 정의한 사용자 변수는 다른 클라이언트에서 보거나 사용할 수 없음
    - 저장하는 값에 의해 자료형이 정해짐
    - 변수를 초기화하지 않은 경우 값은 NULL, 자료형은 String
    
    <br/>
    
    **변수 선언 및 초기화**
    ```sql
    SET @var_name = expr;
    SET @var_name := expr;
    SELECT @var_name := expr;
    ```
    SET 이외의 명령문에서는 `=`가 비교연산자로 취급되기 때문에 대입연산자인 `:=`를 사용

<br/>

**지역 변수 (Local Variables)**
    - 저장 프로시저 내에서 사용
    - DECLARE로 먼저 선언 후 사용
    - 지역 변수의 범위는 BEGIN ~ END가 선언된 블록
    - DEFAULT를 지정하지 않으면 NULL
    
    <br/>
    
    **변수 선언 및 초기화**
    ```sql
    BEGIN
        DECLARE var_name 타입 DEFAULT value;

        SET var_name = value;
    END $$
    ```
    
<br/>
<br/>
<br/>

----

# Answer (Draw the triangle 1)

## 1

```sql
DELIMITER $$
CREATE PROCEDURE DrawStar(IN NUM INT)
BEGIN
    WHILE (NUM >= 0) DO
        SELECT  REPEAT("* ", NUM);
        SET NUM = NUM - 1;
    END WHILE;
END $$
DELIMITER ;

CALL DrawStar(20)
```
- 출력할 행의 개수를 입력 파라미터로 사용
- `REPEAT(str, num)` : 문자열(str)을 횟수(num)만큼 반복하는 함수

<br/>
<br/>

## 2

```sql
SET @number = 21;
SELECT  REPEAT('* ', @number := @number - 1)
FROM    information_schema.tables
LIMIT   20;
```

```sql
SET @num = 21;
SELECT  REPEAT('* ', @num := @num - 1)
FROM    information_schema.tables
WHERE   @num > 1;
```
- information_schema.`tables` 대신 `columns`를 써도 돌아가는 것으로 보아 SELECT문을 반복 실행하기 위해 필요한 임의의 테이블
    - 임의의 테이블을 사용하는 것이면 dummy table인 **DUAL을 사용해도 될까?**

        DUAL은 빈 테이블이라 LIMIT이나 WHERE 조건을 주어도 SELECT문이 한번밖에 실행되지 않아 한줄만 출력된다
- LIMIT으로 행 수를 제한하거나, 변수에 조건을 주어 20번 반복하게 함

<br/>
<br/>
<br/>


# Answer (Draw the triangle 2)
반대로 별의 개수를 1부터 하나씩 증가하여 출력하는 문제

## 1

```sql
DELIMITER $$
CREATE PROCEDURE DrawStars(IN num INT)
BEGIN
    DECLARE i INT DEFAULT 1;
    
    WHILE (i <= num) DO
        SELECT  REPEAT("* ", i);
        SET i = i + 1;
    END WHILE;
END $$
DELIMITER ;

CALL DrawStars(20)
```

## 2

```sql
SET @num = 0;
SELECT  REPEAT('* ', @num := @num + 1)
FROM    information_schema.tables
WHERE   @num < 20;
```

<br/>
<br/>
<br/>
