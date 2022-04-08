---
title: "[MySQL][Hacker Rank] Symmetric Pairs"
excerpt: "해커랭크 SQL - Advanced Join (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-03
last_motified_at:
---
<br/>
<br/>

# Problem
You are given a table, Functions, containing two columns: X and Y.

![image](https://user-images.githubusercontent.com/85720248/162437247-684f115f-f491-4895-b937-b35acfc811b6.png)

Two pairs (X1, Y1) and (X2, Y2) are said to be symmetric pairs if X1 = Y2 and X2 = Y1.

Write a query to output all such symmetric pairs in ascending order by the value of X. List the rows such that X1 ≤ Y1.

<br/>

## 문제 설명
- x, y가 바뀐 대칭쌍이 있는 x, y를 찾는 문제
- 단 x ≤ y 인 것으로 출력

<br/>
<br/>
<br/>

# Solution
대칭쌍 중 하나의 x,y 자리를 바꿔주면 같은 값이 된다.

x,y로 그룹화했을 때 대칭쌍이 같은 그룹이 될 수 있도록 x,y를 맞춰주고, 그룹화 후 개수가 2개 이상인지 확인해준다.

`LEAST`, `GREATEST` 함수를 사용해 x,y 중 작은 값을 x, 큰 값을 y로 지정해준다.
즉, x1=y2(x1 ≤ y1), y1=x2인 대칭쌍 (x1, y1), (x2, y2)을 (x1, y1), (y2, x2)로 바꿔준다.

<br/>
<br/>
<br/>

# Answer

```sql
SELECT  x, y
FROM    (SELECT LEAST(x, y) x, GREATEST(x, y) y FROM    Functions) F
GROUP BY    x, y
HAVING  COUNT(*) > 1
ORDER BY    x
```

<br/>
<br/>
<br/>
