---
title: "[MySQL][Hacker Rank] Challenges"
excerpt: "해커랭크 SQL - Basic Join (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, Subquery]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-01
last_motified_at:
---
<br/>
<br/>

# Problem
Julia asked her students to create some coding challenges. Write a query to print the hacker_id, name, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.
<br/>


**Input Format**

The following tables contain data on the wands in Ollivander's inventory:

- **Hackers**: The <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the hacker, and <mark style='background-color: #E5F0FD'>name</mark> is the name of the hacker.
- **Challenges**: The <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge, and <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the student who created the challenge.

<br/>

## 문제 설명
해커id와 만든 챌린지 id가 주어지고, 해커별로 만든 챌린지 개수를 구하는 문제이다.

hacker_id, name, 만든 챌린지 개수를 출력한다.

만든 챌린지 개수가 큰 순으로, hacker_id 순으로 정렬한다.

단, 여러 해커의 만든 개수가 같을 때 그 개수가 최대 개수보다 적다면 결과에서 제외한다.

<br/>
<br/>
<br/>

# Solution
여러 해커의 만든 개수가 같을 때 제외하라고 하면 간단하게 풀리지만, 최대 개수인 경우는 모두 출력핼야하기 때문에 까다로워진 문제이다.

①만든 개수가 같지 않은 경우와 ②최대 개수인 경우를 각각 조건으로 걸어줘야 한다.

해커별로 만든 챌린지 수를 서브쿼리로 여러번 사용하면 된다.

<br/>

## 서브쿼리
서브쿼리는 하나의 SQL문에 포함되어 있는 또 다른 SQL문이 중첩된 질의를 말한다.

즉, 서브쿼리의 실행 결과를 메인 쿼리의 데이터 검색 조건으로 이용하는 것이라고 이해할 수 있다.

** 서브쿼리 사용시 괄호로 감싸서 사용해야 한다.
<br/>

1. 위치에 따른 분류

    |명칭|위치|설명|
    |---|---|---|
    |**스칼라 부속질의**|SELECT 절|단일 값을 반환|
    |**인라인 뷰**|FROM 절|결과를 뷰(View) 형태로 반환|
    |**중첩질의**|WHERE 절|연산자와 함께 사용|


2. 반환되는 데이터 형태에 따른 분류

    - **단일행 서브쿼리** : 실행 결과로 하나의 행을 반환. 단일행 비교 연산자(=, <, >, <> 등)와 함께 사용
    - **다중행 서브쿼리** : 실행 결과로 여러 행을 반환. 다중행 비교 연산자(IN, ALL, ANY, SOME, EXISTS)와 함께 사용
    - **다중컬럼 서브쿼리** : 실행 결과로 여러 컬럼을 반환.
<br/>

3. 동작 방식에 따른 분류
    - **연관 서브쿼리** : 메인쿼리의 칼럼을 가지고 있는 형태의 서브쿼리. 일반적으로 메인쿼리가 먼저 실행되어 읽혀진 데이터를 서브쿼리에서 조건이 맞는지 확인하고자 할 때 주로 사용
    - **비연관 서브쿼리** : 서브쿼리가 메인쿼리 칼럼을 가지고 있지 않은 형태로 메인쿼리에 값을 제공하기 위한 목적으로 사용

<br/>
<br/>
<br/>

# Answer

## 1

```sql
SELECT  H.hacker_id, H.name, C.cnt
FROM    Hackers H
        JOIN
        (SELECT hacker_id, COUNT(*) cnt
        FROM    Challenges
        GROUP BY    hacker_id) C
        ON H.hacker_id = C.hacker_id
WHERE   (cnt = (SELECT   MAX(tmp.cnt)
              FROM  (SELECT hacker_id, COUNT(*) cnt
                     FROM    Challenges
                     GROUP BY    hacker_id) tmp)) OR
        (cnt IN (SELECT  cnt
               FROM (SELECT hacker_id, COUNT(*) cnt
                     FROM    Challenges
                     GROUP BY    hacker_id) tmp
               GROUP BY cnt
               HAVING   COUNT(*) = 1))
ORDER BY    C.cnt DESC, H.hacker_id;
```
1. 해커별로 만든 챌린지 수(cnt)를 만든 인라인뷰와 Hackers 테이블을 조인한다.
2. WHERE절에서 cnt가 최대값인 경우이거나 cnt 값이 중복되지 않는 경우만 선택한다.
    - 최대cnt는 한 값으로 리턴되는 단일행 서브쿼리이므로 `=`연산자 사용
    - 중복되지 않는 cnt는 여러 개의 행이 리턴되는 다중행 서브쿼리이기 때문에 `IN`연산자 사용
        - 중복되지 않는 cnt는 cnt별로 그룹화하고 HAVING에 개수가 1개인 것만 선택 조건을 걸어준다.

<br/>

## 2

```sql
SELECT  H.hacker_id, H.name, COUNT(*) cnt
FROM    Hackers H
        JOIN
        Challenges C ON H.hacker_id = C.hacker_id
GROUP BY    H.hacker_id, H.name
HAVING  (cnt = (SELECT  MAX(cnt)
                FROM (SELECT COUNT(*) cnt
                      FROM   Challenges
                      GROUP BY   hacker_id) tmp)) OR
        (cnt IN (SELECT   cnt
                 FROM  (SELECT COUNT(*) cnt
                        FROM   Challenges
                        GROUP BY   hacker_id) tmp
                 GROUP BY  cnt
                 HAVING    COUNT(*) = 1))
ORDER BY    cnt DESC, H.hacker_id;
```
첫번째 방법은 cnt 테이블을 먼저 만들고 조건을 걸었기 때문에 WHERE절을 사용했지만, 두번째 방법은 cnt 테이블을 먼저 만들지 않고 기존 테이블만 JOIN한 뒤 cnt를 구하기 위해 그룹화하면서 조건을 걸어주는 것이므로 HAVING절에 조건을 써주었다.


<br/>
<br/>
