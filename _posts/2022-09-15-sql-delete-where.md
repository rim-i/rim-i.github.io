---
title: "[MySQL] DELETE문에서 서브쿼리 (LeetCode 196. Delete Duplicate Emails)"
excerpt: ""

categories:
    - SQL
tags:
    - [MySQL, Subquery, LeetCode]

toc: true
toc_sticky: true
toc_label:

date: 2022-09-15
last_motified_at:
---
<br/>
<br/>

# Problem
가장 작은 id만 남기고 중복된 메일을 제거하기  
[Table]  
- **Person** : id(PK), email

WHERE절에 서브쿼리를 넣어서 삭제할 데이터를 지정해주려고 하는데 에러 발생

<br/>
<br/>
<br/>


# Error

```sql
DELETE  FROM    Person
WHERE   id NOT IN (SELECT  MIN(id)
                   FROM    Person
                   GROUP BY    email)
```

1. 이메일로 그룹핑하여 가장 작은 값의 id를 서브쿼리로 선택하고 -> 남길 데이터  
2. WHERE절에서 `NOT IN`으로 남길 id에 포함되지 않는 id만 선택 -> 지워짐

이렇게 서브쿼리를 사용하여 문제를 풀려고 했는데 에러가 발생했다.

You can't specify target table 'Person' for update in FROM clause
{: .notice--primary} 

**FROM절의 테이블을 참조할 수 없다**는 에러로, <mark style='background-color: #E5F0FD'>**DELETE문에서는 동일한 테이블을 직접 참조할 수 없다고 한다.**</mark>

따라서, 서브쿼리를 한번 더 넣어 인라인뷰로 참조할 수 있도록 해야한다.

<br/>
<br/>
<br/>

# Answer

```sql
DELETE FROM Person
WHERE id NOT IN (SELECT min_id
                 FROM    (SELECT  MIN(id) min_id
                          FROM    Person
                          GROUP BY    email) TMP)
```
처음 작성했던 서브쿼리를 tmp 임시 테이블로 만들고, 서브쿼리를 하나 더 써서 임시테이블에서 해당 속성을 가져오도록 했다.


<br/>
<br/>
<br/>
