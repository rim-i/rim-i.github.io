---
title: "[MySQL][Hacker Rank] SQL Project Planning"
excerpt: "해커랭크 SQL - Advanced Join (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, Rownum]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-05
last_motified_at:
---
<br/>
<br/>

# Problem
You are given a table, Projects, containing three columns: Task_ID, Start_Date and End_Date. It is guaranteed that the difference between the End_Date and the Start_Date is equal to 1 day for each row in the table.

![image](https://user-images.githubusercontent.com/85720248/162563323-1c04a250-d914-4643-91f2-6925934ea9de.png)

If the End_Date of the tasks are consecutive, then they are part of the same project. Samantha is interested in finding the total number of different projects completed.

Write a query to output the start and end dates of projects listed by the number of days it took to complete the project in ascending order. If there is more than one project that have the same number of completion days, then order by the start date of the project.

<br/>

## 문제 설명
- start_date와 end_date는 1일 차이
- end_date가 연속적이라면 같은 프로젝트
- 각 프로젝트의 start_date, end_date 출력
- 프로젝트에 소요된 기간(end_date - start_date)가 큰 순, start_date가 빠른 순

<br/>
<br/>
<br/>

# Solution

## 1

날짜 순으로 붙어있는 두 행이 같은 프로젝트라면 **앞 행의 end_date와 뒷 행의 start_date가 같다**

날짜 순으로 정렬했을 때 앞뒤로 있는 행의 start_date와 end_date를 비교해 프로젝트 시작일과 종료일을 찾고, 시작 일의 개수와 종료 일의 개수가 모두 프로젝트 개수로 동일하므로 각각 행번호를 매겨 조인해준다.

1. 시작일 찾기
    - 지정된 컬럼의 다음 행 값을 가져오는 `LAG()` 함수를 이용
    - 날짜 순으로 정렬하여 이전 행의 end_date와 start_date가 같지 않으면 프로젝트의 시작일
2. 종료일 찾기
    - 지정된 컬럼의 이전 행 값을 가져오는 `LEAD()` 함수를 이용
    - 날짜 순으로 정렬하여 다음 행의 start_date와end_date가 같지 않으면 프로젝트의 종료일
3. `ROW_NUMBER()`를 사용하여 날짜 순으로 행번호를 매겨줌
4. 행번호 기준으로 INNER JOIN

<br/>

### 순위함수
MySQL ver8 이상에서 사용 가능

```sql
RANK() OVER(PARTITION BY col_name1      -- 그룹별로 순위를 매기고 싶을 때
			ORDER BY col_name2 [DESC])  -- 순위를 매길 기준 값
```
- **ROW_NUMBER()** : 1등이 2명이어도 1,2등으로 나뉨 (공동순위 없음)
- **RANK()** : 1등이 2명이면 그 다음 순위는 3등
- **DENSE_RANK()** : 1등이 2명이면 그 다음 순위는 2등
- **NTILE(n)** : n개의 등급으로 나누어 각 등급 번호를 출력 (등급에 속한 데이터의 개수를 비슷하게 맞춤)
- **LAG(expr [,offset] [,default]) / LEAD()** : 지정된 칼럼의 이전, 이후의 행 값을 출력
    - offset : 가져올 행의 위치 (몇 번째 전 또는 후)
    - default : 값이 없는 경우 기본값
- **FIRST_VALUE()** / **LAST_VALUE()** : 각 그룹별 첫 번째 또는 마지막 값 하나만 출력
- **CUM_DIST()** : 상대누적분포 값 반환 (0~1 사이의 값)

`PARTITION BY` 대신 `ORDER BY` 절을 사용해도 될까?
    - `ORDER BY` 를 사용하게 되면 각 그룹별 한줄씩만 출력되기 때문에 원하는 결과값을 뽑아낼 수 없음
    - 그룹 내에서 랭킹이 매겨지는 것과, 그룹으로 묶여지는 것은 다름

순위 함수로 만든 컬럼을 WHERE 조건에서 사용하고 싶다면, 서브 쿼리를 이용해 먼저 순위 함수 컬럼을 뽑고 메인 쿼리에서 필터링 해야 함

<br/>
<br/>

# 2
같은 프로젝트에서 시작일과 종료일이 이어진다. 즉, 첫번째 업무의 종료일과 두번째 업무의 시작일이 같다.

따라서 프로젝트의 시작일은 end_date 컬럼에 같은 값이 없고, 프로젝트의 종료일은 start_date 컬럼에 같은 값이 없다.

1. 시작일과 종료일 찾기
    - 시작일은 end_date 컬럼에 없는 값
    - 종료일은 start_date 컬러메 없는 값
2. 한 프로젝트에서 시작일은 종료일보다 항상 빠르므로 시작일 < 종료일 조건을 주어 JOIN
    - 이때 결과 테이블은 같은 프로젝트의 시작일과 종료일만 결합되지 않음
    - 첫번째 프로젝트의 시작일은 이후의 모든 프로젝트의 종료일과 결합됨
    - 결합된 종료일 중 가장 빠른 날이 해당 프로젝트의 종료일
3. 같은 프로젝트인 시작일과 종료일을 찾기 위해 시작일 별로 가장 작은 종료일 값 선택


<br/>
<br/>
<br/>

# Answer

## 1

```sql
SELECT  A.start_date, B.end_date
FROM    (SELECT  ROW_NUMBER() OVER(ORDER BY start_date) AS num, start_date
         FROM    (SELECT  *, LAG(end_date, 1, 0) OVER(ORDER BY start_date) AS prev_end
                  FROM    Projects) S
         WHERE   start_date != prev_end) A
         INNER JOIN
         (SELECT  ROW_NUMBER() OVER(ORDER BY end_date) AS num, end_date
          FROM    (SELECT  *, LEAD(start_date, 1, 0) OVER(ORDER BY start_date) AS next_start
                   FROM    Projects) E
          WHERE   end_date != next_start) B
          ON A.num = B.num
ORDER BY    (B.end_date - A.start_date), A.start_date;
```
처음엔 `@rownum` 변수를 사용하여 행번호를 매겨줬는데 에러가 났다.
시작일, 종료일 각각 행번호가 매겨지는 것은 확인했는데 JOIN하면 왜 에러가 나는지 모르겠다..

<br/>

## 2

```sql
SELECT  S.start_date, MIN(E.end_date)
FROM    (SELECT start_date
        FROM    Projects
        WHERE   start_date NOT IN (SELECT end_date FROM Projects)) S
        JOIN
        (SELECT end_date
        FROM    Projects
        WHERE   end_date NOT IN (SELECT start_date FROM Projects)) E
        ON S.start_date < E.end_date
GROUP BY    S.start_date
ORDER BY    DATEDIFF(MIN(E.end_date), S.start_date), S.start_date;
```
`DATEDIFF(date1, date2)`에서 date1 > date2 일 때 양수 값

<br/>
<br/>
<br/>
