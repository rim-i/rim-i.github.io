---
title: "[MySQL][Hacker Rank] Weather Observation Station 20"
excerpt: "해커랭크 SQL - Aggregation (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-29
last_motified_at:
---
<br/>
<br/>

# Problem
A median is defined as a number separating the higher half of a data set from the lower half. Query the median of the Northern Latitudes (LAT_N) from STATION and round your answer to  decimal places.
<br/>


**Input Format**

The STATION table is described as follows:
![image](https://user-images.githubusercontent.com/85720248/160548022-76d47e69-dfa4-4bea-b184-1e56befe9f28.png)


<br/>

## 문제 설명
lat_n 컬럼의 중앙값을 구하는 문제이다. 소수점 네번째 자리까지 반올림하여 표시한다.

<br/>
<br/>

# Solution
중앙값은 크기 순으로 나열했을 때 가장 중앙에 위치하는 값이다. 따라서 크기 순에 따라 행번호를 매겨야 한다.

전체 개수 n이 홀수인 경우에는 (n//2 + 1)번째 수, 짝수인 경우에는 (n/2)번째 수와 (n/2 +1)번째 수의 평균이 중앙값이 된다.

예를 들어 10,20,30,40,50의 4개의 값이 있으면 3번째인 30이 중앙값이 된다. 10,20,30,40의 4개의 값이 있으면 2,3번째의 평균인 (20+30)/2=25가 중앙값이 된다.

## 행번호 컬럼 만들기

```sql
SELECT	@rownum:=@rownum + 1 AS rownum, t1.*
FROM	Table1 t1, (SELECT @rownum:=0) RNUM -- 파생되는 테이블은 별칭 필수
ORDER BY	크기 순으로 정렬할 컬럼
```
`@rownum:=0;`는 변수를 초기화해주는 구문이다. SET으로도 가능하지만 FROM, WHERE절에서도 가능하다.

SELECT 전에 변수를 초기화해주지 않으면 값이 NULL인 상태이기 때문에 +1을 해도 계속 NULL이 되므로 초기화는 필수이다. 또한, 초기화 쿼리가 없으면 조회할 때마다 계속 값이 증가하는 문제가 생길 수 있다.

`@rownum:= @rownum+ 1 AS no(별칭)`은 행을 불러올 때마다 1을 더해서 출력하라는 구문이다.

```sql
SELECT  @rownum := @rownum + 1 AS rownum, S.lat_n
FROM    Station S, (SELECT @rownum:=0) RNUM
ORDER BY    S.lat_n
```
![image](https://user-images.githubusercontent.com/85720248/160551943-d76100ec-8b34-44c9-89ea-14f980815c8b.png)

lat_n 값을 기준으로 행번호가 잘 매겨진 것을 볼 수 있다.

<br/>
<br/>

# Answer

```sql
SELECT  ROUND(AVG(lat_n), 4)
FROM    (SELECT  @rownum := @rownum + 1 AS rownum, S.lat_n
        FROM    Station S, (SELECT @rownum:=0) RNUM
        ORDER BY    S.lat_n) AS L
WHERE   IF(@rownum % 2 = 0, rownum IN (@rownum DIV 2, @rownum DIV 2 + 1), rownum = @rownum DIV 2 + 1)
```

행번호가 rownum 컬럼으로 들어간 테이블에서 WHERE절에서 IF문을 사용하여 전체 행 개수가 짝수인지 홀수인지에 따라 필요한 rownum만 추출했다.

나머지 연산은 %, 몫 연산은 DIV (MySQL에는 //가 안된다!)

<br/>
<br/>
