---
title: "[Hacker Rank][MySQL] Occupation"
excerpt: "해커랭크 SQL - Advanced Select (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-26
last_motified_at:
---
<br/>
<br/>

# Problem
Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. The output column headers should be Doctor, Professor, Singer, and Actor, respectively.

**Note**: Print NULL when there are no more names corresponding to an occupation.
<br/>
<br/>

**Input Format**

The **OCCUPATIONS** table is described as follows:

![image](https://user-images.githubusercontent.com/85720248/160093228-a2a85be2-e7d1-458f-a67f-541af2a54b9b.png)

Occupation will only contain one of the following values: Doctor, Professor, Singer or Actor.

<br/>

## 문제 설명
이름과 직업이 컬럼으로 있는 테이블에서 직업별로 컬럼을 만들어 각 컬럼에서 알파벳 순으로 이름을 정렬하는 문제이다.
각 컬럼에서 더 이상 존재하는 데이터가 없으면 NULL로 출력한다.

<br/>

## Example
**Sample Input**

![image](https://user-images.githubusercontent.com/85720248/160094253-56dbca0c-c342-4411-8ccd-06f3a332ee03.png)
<br/>

**Sample Output**

```py
Jenny    Ashley     Meera  Jane
Samantha Christeen  Priya  Julia
NULL     Ketty      NULL   Maria
```
<br/>

**Explanation**

- 각 컬럼은 Doctor, Professor, Singer, Actor의 이름들을 알파벳 순으로 정렬한 것
- 직업 별 이름의 최대 개수보다 데이터가 적은 컬럼에서 빈 값은 NULL로 채움 
    - 직업별 데이터 수 : Dotor 2개, Professor 3개, Singer 2개, Actor 3개
    - 여기서는 Professor와 Actor 컬럼의 데이터가 3개로 가장 많으므로 기준이 된다

<br/>
<br/>


# Solution

## STEP1
```SQL
SELECT  IF(Occupation = 'Doctor', name, NULL),
        IF(Occupation = 'Professor', name, NULL),
        IF(Occupation = 'Singer', name, NULL),
        IF(Occupation = 'Actor', name, NULL)
FROM    Occupations
ORDER BY    name
```
![image](https://user-images.githubusercontent.com/85720248/160137425-65d7e211-03c7-44bf-8a8a-c5b512190412.png)

각 직업에서 알파벳 순으로 행번호를 매겼을 때 같은 행번호끼리 한 행으로 출력되어야 하는데, Occupations 테이블에서 한줄씩 읽어오기 때문에 같은 행에 출력되지 않고 있다.

직업별로 행번호를 먼저 매겨주는 것이 필요함을 알 수 있다.
<br/>

## STEP2

```sql
SET @d:=0, @p:=0, @s:=0, @a:=0;

SELECT  name, occupation,
        CASE WHEN occupation = 'Doctor' THEN @d:=@d+1
        WHEN occupation = 'Professor'THEN @p:=@p+1
        WHEN occupation = 'Singer' THEN @s:=@s+1
        WHEN occupation = 'Actor' THEN @a:=@a+1
        END AS num
FROM    Occupations
ORDER BY    name
```
![image](https://user-images.githubusercontent.com/85720248/160139461-e6eadcfc-d563-40f5-b497-09ca2a0a0179.png)
<br/>
직업별로 카운트 할 변수를 만들고 조건문을 사용해 해당 직업인 경우에만 +1 하도록 했다.

결과를 보면 직업별로 rownum이 잘 매겨진 것을 볼 수 있다.

<br/>

## STEP3

```sql
SET @d:=0, @p:=0, @s:=0, @a:=0;

SELECT  MIN(CASE WHEN Occupation = 'Doctor' THEN name END),
        MIN(CASE WHEN Occupation = 'Professor' THEN name END),
        MIN(CASE WHEN Occupation = 'Singer' THEN name END),
        MIN(CASE WHEN Occupation = 'Actor' THEN name END)
FROM    (SELECT  name, occupation,
                CASE WHEN occupation = 'Doctor' THEN @d:=@d+1
                WHEN occupation = 'Professor'THEN @p:=@p+1
                WHEN occupation = 'Singer' THEN @s:=@s+1
                WHEN occupation = 'Actor' THEN @a:=@a+1
                END AS num
        FROM    Occupations
        ORDER BY    name) t
GROUP BY t.num
```
rownum을 기준으로 group by해서 직업별 이름을 출력한다.

조건문을 사용해서 각 컬럼에 맞는 직업을 선택하고, group by를 사용한 경우 집계함수를 사용해야 하므로 `MIN()`을 사용한다.

여기서 `MIN()`은 함수로 작은값을 구하기 위해서 사용하는 것이 아니라 출력을 위해 사용하는 것이다. 직업별로 rownum을 매겼기 때문에 rownum을 기준으로 그룹화하면 각 직업당 하나의 이름만 나온다. 따라서 `MIN()`나 `MAX()` 어떤 것을 사용해도 무방하다.

문자열에 MIN, MAX 함수를 사용하는 경우 알파벳 순으로 작은 것(앞에 있는 것), 큰 것(뒤에 있는 것)을 출력해준다.

<br/>
<br/>


# Answer

```sql
SET @d:=0, @p:=0, @s:=0, @a:=0;

SELECT  MIN(CASE WHEN Occupation = 'Doctor' THEN name END),
        MIN(CASE WHEN Occupation = 'Professor' THEN name END),
        MIN(CASE WHEN Occupation = 'Singer' THEN name END),
        MIN(CASE WHEN Occupation = 'Actor' THEN name END)
FROM    (SELECT  name, occupation,
                CASE WHEN occupation = 'Doctor' THEN @d:=@d+1
                WHEN occupation = 'Professor'THEN @p:=@p+1
                WHEN occupation = 'Singer' THEN @s:=@s+1
                WHEN occupation = 'Actor' THEN @a:=@a+1
                END AS num
        FROM    Occupations
        ORDER BY    name) t
GROUP BY t.num
```

![image](https://user-images.githubusercontent.com/85720248/160150410-dbab5172-c480-4e4f-a186-bfcd068dff4a.png)
