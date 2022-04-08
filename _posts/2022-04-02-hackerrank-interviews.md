---
title: "[MySQL][Hacker Rank] Interviews"
excerpt: "해커랭크 SQL - Advanced Join (Hard)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, JOIN]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-02
last_motified_at:
---
<br/>
<br/>

# Problem
Samantha interviews many candidates from different colleges using coding challenges and contests. Write a query to print the contest_id, hacker_id, name, and the sums of total_submissions, total_accepted_submissions, total_views, and total_unique_views for each contest sorted by contest_id. Exclude the contest from the result if all four sums are .
<br/>

**Note**: A specific contest can be used to screen candidates at more than one college, but each college only holds  screening contest.
<br/>


**Input Format**

The following tables hold interview data:

- **Contests**: The <mark style='background-color: #E5F0FD'>contest_id</mark> is the id of the contest, <mark style='background-color: #E5F0FD'>hacker_id</mark> is the id of the hacker who created the contest, and <mark style='background-color: #E5F0FD'>name</mark> is the name of the hacker. 
- **Colleges**: The <mark style='background-color: #E5F0FD'>college_id</mark> is the id of the college, and <mark style='background-color: #E5F0FD'>contest_id</mark> is the id of the contest that Samantha used to screen the candidates. 
- **Challenges**: The <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge that belongs to one of the contests whose contest_id Samantha forgot, and <mark style='background-color: #E5F0FD'>college_id</mark> is the id of the college where the challenge was given to candidates.
- **View_Stats**: The <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge, <mark style='background-color: #E5F0FD'>total_views</mark> is the number of times the challenge was viewed by candidates, and <mark style='background-color: #E5F0FD'>total_unique_views</mark> is the number of times the challenge was viewed by unique candidates.
- **Submission_Stats**: The <mark style='background-color: #E5F0FD'>challenge_id</mark> is the id of the challenge, <mark style='background-color: #E5F0FD'>total_submissions</mark> is the number of submissions for the challenge, and <mark style='background-color: #E5F0FD'>total_accepted_submission</mark> is the number of submissions that achieved full scores.


<br/>

## 문제 설명
coding challenge와 contest와 통해 여러 대학의 후보자들을 인터뷰한 정보를 종합하는 문제이다.

- contest_id, hacker_id, name, 여러합계 출력
- contest_id 기준으로 정렬
- 모든 합이 0인 경우는 제외

한 contest는 여러 대학에서 사용될 수 있지만, 한 대학에서는 하나의 contest만 열린다. 또한, 한 대학에서 여러 challenge가 진행될 수 있다.

<br/>
<br/>
<br/>

# Solution
contest 별 total_submissions, total_accepted_submissions, total_views, total_unique_views 값의 합계를 구해야하고, 이 값들이 들어있는 테이블인 View_Stats, Submission_Stats는 challenge 기준으로 되어있다.

1. 각 contest에 속한 challenge를 확인하기 위해 Contest, Collges, Challenges 테이블 JOIN
2. Challenges 테이블의 challenge_id를 기준으로 View_Stats과 Submission_Stats 테이블을 LEFT JOIN
    - View_Stats에만 데이터가 있거나 Submission_Stats에만 데이터가 있을 경우를 위해 Challenges 테이블을 기준으로 LEFT OUTER JOIN
    - View_Stats, Submission_Stats 테이블 challenge_id에 중복값이 존재 → Challenges 테이블과 <mark style='background-color: #E5F0FD'>1:N관계</mark>
    - 테이블 그대로 두 번 LEFT JOIN 하면 같은 challenge_id에 대해서 중복된 row만큼 total_views, total_unique_views, total_submissions, total_accepted_submissions의 합이 몇배가 됨
    - 즉, 하나의 challenge_id가 View_Stats, Submission_Stats에 2개씩 있다고 가정하면 조인 후에 2배로 늘어남 -> 합계 값도 2배

        ![image](https://user-images.githubusercontent.com/85720248/162394801-39b48c03-b549-4a83-b10a-9afb377d341a.png)
    - 따라서 View_Stats, Submission_Stats 테이블에서 challenge_id 별로 합을 구하는 서브쿼리를 먼저 작성하여 1:1 관계로 만들고 조인해야함
3. contest_id, hacker_id, name으로 그룹화하고 전체 합이 0이 아닌 데이터만 뽑는다


<br/>

## 테이블 간의 관계
실제로 참여하는 개별 개체 수

1. **<mark style='background-color: #E5F0FD'>1:1 관계</mark>**
    
    하나의 레코드가 다른 테이블의 레코드 한 개와 연결
    
    EX) 한 학생이 한 개의 사물함을 사용하고, 한 개의 사물함은 한 학생만 이용함
<br/>
   
2. **<mark style='background-color: #E5F0FD'>1:N 관계</mark> / <mark style='background-color: #E5F0FD'>N:1 관계</mark>**
    
    하나의 레코드가 다른 테이블의 여러 개의 레코드와 연결
    
    EX) 한 학생은 한 학과에 소속되고, 한 학과에는 여러 명의 학생이 소속되어 있음
<br/>
   
3. **<mark style='background-color: #E5F0FD'>N:M 관계</mark>**
    
    여러 개의 레코드가 다른 테이블의 여러 개의 레코드와 연결
    
    EX) 한 학생은 여러 개의 동아리에 가입할 수 있고, 한 동아리에는 여러 학생이 가입할 수 있음

<br/>

## 1:N 관계에서 LEFT OUTER JOIN
**LEFT JOIN**
- 왼쪽 테이블을 기준으로 일치하는 행만 결합되고, 일치하지 않는 부분은 null 값으로 채워짐
- 왼쪽 테이블의 한 개의 레코드에 여러 개의 오른쪽 테이블 레코드가 일치할 경우, 해당 왼쪽 레코드가 여러번 표시됨

<br/>

**전체 ROW 수의 변화**

LEFT OUTER JOIN 에서 기준이 되는 테이블의 행 수가 조인 후에도 그대로 일 것이라고 생각할 수 있지만, <span style="color:#CD3B3B">1:1 또는 N:1 관계를 갖는 경우에만 행 수가 그대로</span>이다.

<span style="color:#CD3B3B">1:N 관계에서는 기준 테이블의 데이터가 중복되어 행 수가 뻥튀기</span> 된다.

<br/>

**중복 제거 방법**
1. DISTINCT
    ```sql
    SELECT  *
    FROM    A
            LEFT OUTER JOIN
            (SELECT DISTICT xx  FROM    B)
            ON A.xx = B.xx
    ```
    DISTINCT는 중복된 컬럼 중 가장 첫번째 값을 가져오기 때문에 원하는 특정 값이 있다면 MIN(xx) 등을 사용하면 된다
<br/>

2. GROUP BY
    ```sql
    SELECT  *
    FROM    A
            LEFT OUTER JOIN
            (SELECT xx, MAX(yy)  FROM    B
            GROUP BY    xx)
            ON A.xx = B.xx
    ```

<br/>
<br/>
<br/>

# Answer

```sql
SELECT  Con.contest_id, Con.hacker_id, Con.name, SUM(S.ts), SUM(S.tas), SUM(V.tv), SUM(V.tuv)
FROM    Contests Con
        JOIN Colleges Col ON (Con.contest_id = Col.contest_id)
        JOIN Challenges Cha ON (Col.college_id = Cha.college_id)
        LEFT JOIN (SELECT  challenge_id, SUM(total_views) tv, SUM(total_unique_views) tuv
                FROM    View_Stats
                GROUP BY    challenge_id) V ON (Cha.challenge_id = V.challenge_id)
        LEFT JOIN (SELECT  challenge_id, SUM(total_submissions) ts, SUM(total_accepted_submissions) tas
                FROM    Submission_Stats
                GROUP BY    challenge_id) S ON (Cha.challenge_id = S.challenge_id)
GROUP BY    Con.contest_id, Con.hacker_id, Con.name
HAVING  SUM(S.ts) != 0 OR SUM(S.tas) != 0 OR SUM(V.tv) != 0 OR SUM(V.tuv) != 0
ORDER BY    Con.contest_id;
```




<br/>
<br/>
<br/>
