---
title: "[MySQL][Hacker Rank] Print Prime Numbers"
excerpt: "해커랭크 SQL - Alternative Queries (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, Procedure]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-02
last_motified_at:
---
<br/>
<br/>

# Problem
Write a query to print all prime numbers less than or equal to 1000. Print your result on a single line, and use the ampersand () character as your separator (instead of a space).

For example, the output for all prime numbers <= 10 would be:

```sql
2&3&5&7
```

<br/>

## 문제 설명
1000까지의 숫자 중 소수를 찾아 ‘&’로 이어서 출력하기

<br/>
<br/>
<br/>

# Solution
## 1
프로시저에서 while문을 돌려 1000까지의 숫자 중 소수를 찾는다.

소수를 판단하기 위해 다음과 같은 세 가지의 변수가 필요하다
1. i : 판단의 대상이 될 수
2. j : 소수의 정의인 1과 자신 외에 나누어 떨어지는 수가 있는지 판단하기 위한 수 (반절값 보다 큰 수는 어차피 나누어 떨어지지 않으므로 i/2까지로 설정)
3. flag : 소수 판별자. 0으로 설정해두고 나누어 떨어지는 수가 있는 경우 1로 바꿈

프로시저 설명은 [여기](https://rim-i.github.io/sql/hackerrank-draw-the-triangle/) 참조

<br/>

## 2
첫번째는 약수가 있는지 확인하여 소수를 판별한 풀이이고, 두번째는 배수를 지워 소수만 남기는 풀이이다.

1. 2부터 1000까지의 수가 담긴 테이블 생성
    - 1은 소수가 아니기 때문에 포함하지 않음
    - 소수인지 판별할 대상
2. 배수를 이용하여 소수가 아닌 수 삭제
    - 2의 배수부터 500의 배수까지 삭제 (501부터는 2배한 수가 1000보다 크므로 판단하는 의미 없음)
    - 2배수, 3배수, ···의 수를 삭제하는 것이기 때문에 나누었을 때 몫은 2이상, 나머지는 0이어야 함

### 테이블 생성 및 데이터 삽입, 삭제
1. 테이블 생성
    ```sql
    CREATE TABLE 테이블명(
        컬럼명 데이터타입(보이는 자리수) 조건,
        컬럼명 데이터타입(보이는 자리수) 조건,
        PRIMARY KEY(컬럼명)
    );
    ```
<br/>

2. 데이터 삽입
    ```sql
    INSERT INTO 테이블명 (컬럼1, 컬럼2, 컬럼3) VALUES (값1, 값2, 값3)
    ```
<br/>

3. 데이터 삭제
    ```sql
    DELETE FROM 테이블명 WHERE 조건절
    ```
<br/>

### GROUP_CONCAT

```sql
GROUP_CONCAT(칼럼명 separator '구분자') FROM 테이블명 (GROUP BY 그룹명)
```
- 서로 다른 행에 있는 데이터를 한 줄로 합쳐주는 함수
- 각 행의 데이터를 구분자로 이어줌
- GROUP을 설정하지 않으면 모든 행을 합쳐줌

<br/>
<br/>
<br/>

# Answer
## 1

```sql
DELIMITER $$
CREATE PROCEDURE find_prime(IN NUM INT)
BEGIN   
    DECLARE i, j, flag INT;
    DECLARE return_txt VARCHAR(1000);

    SET i = 2;
    SET return_txt = '';

    WHILE i < NUM DO
        SET j = 2;
        SET flag = 0;
        
        WHILE j <= (i / 2) DO
            IF MOD(i,j) = 0 THEN SET flag = 1;
            END IF;
            SET j = j + 1;
        END WHILE;

        IF (flag = 0) THEN 
            SET return_txt = CONCAT(return_txt, i, '&');
        END IF;
        
        SET i = i + 1;
    END WHILE;
    
    SET return_txt = SUBSTR(return_txt, 1, LENGTH(return_txt) - 1);
    SELECT return_txt;
END$$
DELIMITER ;

CALL    find_prime(1000);
```
<br/>

## 2

```sql
CREATE TABLE Numbers(num INT PRIMARY KEY); -- 빈테이블 생성

-- 2부터 1000까지 채워주는 프로시저
DELIMITER $$
CREATE PROCEDURE MakeNum(IN end_num INT)
BEGIN
    DECLARE i INT DEFAULT 2;
    WHILE (i <= end_num) DO
        INSERT INTO Numbers VALUES (i);
        SET i = i + 1;
    END WHILE;
END $$
DELIMITER ;

-- 배수만 삭제하는 프로시저
DELIMITER $$
CREATE PROCEDURE DeleteNonPrime(IN end_num INT)
BEGIN
    DECLARE j INT DEFAULT 2;
    WHILE (j <= end_num / 2) DO
        DELETE FROM Numbers WHERE num % j = 0 AND num / j > 1;
        SET j = j + 1;
    END WHILE;
END $$
DELIMITER ;

-- 프로시저 실행
CALL MakeNum(1000);
CALL DeleteNonPrime(1000);

-- 구분자로 이어서 출력
SELECT  GROUP_CONCAT(num separator '&')
FROM    Numbers;
```


<br/>
<br/>
<br/>
