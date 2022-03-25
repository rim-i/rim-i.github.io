---
title: "[MySQL][Hacker Rank] Type of Triangle"
excerpt: "해커랭크 SQL - Advanced Select (Easy)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, IF]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-25
last_motified_at:
---

# Problem
Write a query identifying the type of each record in the **TRIANGLES** table using its three side lengths. Output one of the following statements for each record in the table:

- **Equilateral**: It's a triangle with  sides of equal length.
- **Isosceles**: It's a triangle with  sides of equal length.
- **Scalene**: It's a triangle with  sides of differing lengths.
- **Not A Triangle**: The given values of A, B, and C don't form a triangle.
<br/>
<br/>

**Input Format**

The **TRIANGLES** table is described as follows:

![image](https://user-images.githubusercontent.com/85720248/160077784-10c1b01b-b042-43e2-9ec2-cf39073f1928.png){: .align-left}

<br/>

## 문제 설명
3변의 길이가 주어지고, 어떤 삼각형인지 판별하는 문제이다.
IF문을 사용하여 정삼각형, 이등변삼각형, 삼각형, 삼각형이 아닌 것을 구분해서 출력해야한다.

삼각형의 성립 조건 : 한 변의 길이가 다른 두 변의 길이의 합보다 작아야 한다.
즉, a,b,c 세 변에 대하여 a < b + c가 되어야 한다.

# Solution
조건문을 사용할 때는 조건의 순서를 잘 정해줘야 한다. 첫번째 조건에 걸리지 않으면 두번째 조건, 두번째 조건에도 걸리지 않으면 세번째 조건으로 넘어가는 필터 같은 구조이기 때문이다. 따라서, 범위가 좁은 조건부터 써주는 것이 좋다.

우선, 삼각형이 아니면 어떤 삼각형인지 판단하는 것이 의미가 없으므로 삼각형인지부터 조건으로 걸어준다.

그 다음으로는 정삼각형, 이등변삼각형, 그냥 삼각형 순으로 조건을 걸어준다.
만약, 이등변삼각형을 먼저 조건으로 걸어주게 되면 정삼각형도 이등변삼각형에 속하기 때문에 나온 결과값에서 중첩으로 조건문을 다시 사용해줘야하기 때문이다.

## MySQL 조건문
### 1. IF
```MySQL
IF(조건문, 참일 때 값, 거짓일 때 값)
```
엑셀의 IF()함수와 같다. 조건이 여러개일 때 중첩해서 사용이 가능하지만, 코드가 지저분해지기 때문에 아래의 CASE문을 사용하는 것이 좋다.

### 2. CASE WHEN
```MySQL
CASE
	WHEN 조건1 THEN 결과값1
	WHEN 조건2 THEN 결과값2
	WHEN 조건N THEN 결과값N
	ELSE 결과값
END
```
WHEN ~ THEN은 여러 번 사용할 수 있으며, 마지막의 ELSE는 모든 조건이 아닐 때를 지정한다.

# Answer
```MySQL
SELECT  CASE
            WHEN (A + B <= C) OR (A + C <= B) OR (B + C <= A) THEN 'Not A Triangle'
            WHEN A = B AND B = C THEN 'Equilateral'
            WHEN (A = B AND B != C) OR (B = C AND A != B) OR (A = C AND A != B) THEN 'Isosceles'
            ELSE 'Scalene'
        END
FROM    Triangles;
```
첫번째 WHEN에서 삼각형이 아닌 것들이 참이 되어 **Not A Triangle**이 출력된다.
두번째 WHEN에서 A = B이고 B = C인 것, 즉 A = B = C인 정삼각형이면 참이 되어 **Not A Equilateral**이 출력된다.
세번째 WHEN에서 두 변의 길이는 같지만 나머지 한변의 길이는 다른 이등변 삼각형이면 참이 되어 **Isosceles**이 출력된다.
이 모든 조건에도 걸리지 않으면 그냥 삼각형 **Scalene**이다.
