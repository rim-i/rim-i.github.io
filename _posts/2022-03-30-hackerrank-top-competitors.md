---
title: "[MySQL][Hacker Rank] Top Competitors"
excerpt: "해커랭크 SQL - Basic Join (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, JOIN]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-30
last_motified_at:
---
<br/>
<br/>

# Problem
Julia just finished conducting a coding contest, and she needs your help assembling the leaderboard! Write a query to print the respective hacker_id and name of hackers who achieved full scores for more than one challenge. Order your output in descending order by the total number of challenges in which the hacker earned a full score. If more than one hacker received full scores in same number of challenges, then sort them by ascending hacker_id.
<br/>


**Input Format**

The following tables contain contest data:

- **Hackers**: The <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the hacker, and <mark style='background-color: #E5F0FD'>name</mark> is the name of the hacker.
- **Difficulty**: The <mark style='background-color: #E5F0FD'>difficult_level</mark> is the level of difficulty of the challenge, and <mark style='background-color: #E5F0FD'>score</mark> is the score of the challenge for the difficulty level. 
- **Challenges**: The <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge, the <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the hacker who created the challenge, and <mark style='background-color: #E5F0FD'>difficulty_level</mark> is the level of difficulty of the challenge.
- **Submissions**: The <mark style='background-color: #E5F0FD'>submission_id</mark> is the id of the submission, <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the hacker who made the submission, <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge that the submission belongs to, and <mark style='background-color: #E5F0FD'>score</mark> is the score of the submission. 

<br/>

## 문제 설명
1개 보다 많은 챌린지에서 만점을 받은 해커의 id와 이름을 구하는 문제이다.

만점받은 챌린지 개수가 많은 순으로, 만점받은 챌린지 개수가 같은 경우 해커id 순으로 정렬한다.

<br/>
<br/>
<br/>

# Solution
Submission 테이블의 score(해커가 받은 점수)와 Difficulty 테이블의 score(만점 점수)를 비교하여 만점 받은 챌린지만 남기고, 해커별로 개수를 세면 된다.

테이블 간 연결되는 key 컬럼이다.

[Submission]--challenge_id--[Challenges]--difficulty_level--[Difficulty]

Submission 기준으로 순서대로 LEFT JOIN을 해야 한다. 

<br/>

## LEFT JOIN
왼쪽 테이블을 기준으로 테이블이 붙는 조인이다. 즉, 왼쪽 테이블에서는 모든 값을, 오른쪽 테이블에서는 왼쪽 테이블과 일치되는 값만 반환한다.

3개 이상의 테이블을 LEFT JOIN할 때는 조인하는 테이블의 순서가 매우 중요하다. 어떤 순서로 테이블을 조인하는지에 따라 결과물이 달라질 수 있다. 따라서 <mark style='background-color: #E5F0FD'>가장 많은 row를 가져와야 할 테이블</mark>을 먼저 적어줘야 한다.

```sql
SELECT	*
FROM	A
        LEFT JOIN B
        ON A.id = B.id
        LEFT JOIN C 
        ON B.level = C.level
```
이렇게 순서대로 여러번 LEFT JOIN 해주면 된다.

<br/>

```sql
SELECT  @rownum := @rownum + 1 AS rownum, S.lat_n
FROM    Station S, (SELECT @rownum:=0) RNUM
ORDER BY    S.lat_n
```
![image](https://user-images.githubusercontent.com/85720248/160551943-d76100ec-8b34-44c9-89ea-14f980815c8b.png){: width="70%", height="70%"}

Station 테이블에서 lat_n을 제외한 다른 컬럼은 필요하지 않으므로 lat_n만 선택해준다.

lat_n 값을 기준으로 행번호가 잘 매겨진 것을 볼 수 있다.

<br/>
<br/>
<br/>

# Answer

## 1

```sql
SELECT  H.hacker_id, H.name
FROM    (SELECT S.hacker_id, COUNT(*) cnt
        FROM    Submissions S 
                LEFT JOIN Challenges C ON (S.challenge_id = C.challenge_id)
                LEFT JOIN Difficulty D ON (C.difficulty_level = D.difficulty_level)
        WHERE   S.score = D.score
        GROUP BY    S.hacker_id) F
        LEFT JOIN    Hackers H ON (F.hacker_id = H.hacker_id)
WHERE   F.cnt > 1
ORDER BY    F.cnt DESC, H.hacker_id;
```
1. Submission, Challenges, Difficulty 테이블을 순서대로 LEFT JOIN한다.
2. `GROUP BY`로 hacker_id 별 만점 받은 챌린지 개수를 센다. (cnt 컬럼)
3. Hackers 테이블을 조인하여 개수(cnt)가 1개 이상인 것만 출력한다.


## 2

```sql
SELECT  S.hacker_id, H.name
FROM    Hackers H, Difficulty D, Challenges C, Submissions S
WHERE   S.challenge_id = C.challenge_id AND
        C.difficulty_level = D.difficulty_level AND
        S.score = D.score AND
        H.hacker_id = S.hacker_id
GROUP BY    S.hacker_id, H.name
HAVING  COUNT(S.hacker_id) > 1
ORDER BY    COUNT(*) DESC, H.hacker_id;
```
WHERE절에 조건을 주어 연결하는 것도 가능하다.

이때는 만점 개수가 1개 이상 조건을 hacker_id를 기준으로 그룹화한 후 계산하는 것이므로 WHERE절이 아닌 HAVING절에 써주어야 한다.


<br/>
<br/>
