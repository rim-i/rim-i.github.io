---
title: "[MySQL][Hacker Rank] New Companies"
excerpt: "해커랭크 SQL - Advanced Select (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-28
last_motified_at:
---
<br/>
<br/>

# Problem
Amber's conglomerate corporation just acquired some new companies. Each of the companies follows this hierarchy:

![image](https://user-images.githubusercontent.com/85720248/160339784-7eb995e6-ad64-492f-98a9-528ab23b7aab.png)

Given the table schemas below, write a query to print the company_code, founder name, total number of lead managers, total number of senior managers, total number of managers, and total number of employees. Order your output by ascending company_code.

**Note:**
- The tables may contain duplicate records.
- The company_code is string, so the sorting should not be numeric. For example, if the company_codes are C_1, C_2, and C_10, then the ascending company_codes will be C_1, C_10, and C_2.
<br/>
<br/>


**Input Format**

Company: The company_code is the code of the company and founder is the founder of the company.

![image](https://user-images.githubusercontent.com/85720248/160340048-54fe5383-247b-49fa-9d9c-e1b2e5134218.png)

Lead_Manager: The lead_manager_code is the code of the lead manager, and the company_code is the code of the working company. 

![image](https://user-images.githubusercontent.com/85720248/160340119-7aaf1c42-e324-4e38-a868-7b99322e1d8a.png)

Senior_Manager: The senior_manager_code is the code of the senior manager, the lead_manager_code is the code of its lead manager, and the company_code is the code of the working company.

![image](https://user-images.githubusercontent.com/85720248/160340185-5170c1ed-5c53-43bf-ad64-2b50ac77a512.png)

Manager: The manager_code is the code of the manager, the senior_manager_code is the code of its senior manager, the lead_manager_code is the code of its lead manager, and the company_code is the code of the working company. 

![image](https://user-images.githubusercontent.com/85720248/160340255-b7d0147d-80c1-4e15-b327-0edd1972f2c9.png)

Employee: The employee_code is the code of the employee, the manager_code is the code of its manager, the senior_manager_code is the code of its senior manager, the lead_manager_code is the code of its lead manager, and the company_code is the code of the working company. 

![image](https://user-images.githubusercontent.com/85720248/160340311-ed2c2b9c-aec2-46c5-8802-7b4a408915d1.png)


<br/>

## 문제 설명
각 회사의 company_code, founder_name, [lead manager, senior manager, manager, employee]의 수를 구하는 문제이다.

<br/>
<br/>

# Solution
employee 테이블에 윗 직급의 코드까지 다 포함되어 있으므로 employee 테이블을 기준으로 company 테이블을 OUTER JOIN 해준다.

founder - lead manager - senior manager - manager - employee 로 이어지는 계층적 구조이기 때문에 senior manager, manager, employee 테이블에는 중복 데이터가 있을 수 밖에 없다. (예시: 여러 employee를 한 명의 manager가 관리함)

SELECT문에서 중복을 제거하기 위해 `DISTINCT`를 사용하여 정확한 수를 구할 수 있다.


<br/>
<br/>

# Answer

```sql
SELECT  C.company_code, C.founder, 
		COUNT(DISTINCT E.lead_manager_code), COUNT(DISTINCT E.senior_manager_code),
		COUNT(DISTINCT E.manager_code), COUNT(DISTINCT E.employee_code)
FROM    Employee E LEFT OUTER JOIN Company C ON (E.company_code = C.company_code)
GROUP BY    C.company_code, C.founder
ORDER BY    C.company_code;
```

lead_manager_code 중 중복을 제거 한 후 COUNT 해준다.

company_code와 founder을 출력해야하므로 GROUP BY 조건에 두 컬럼 다 써줘야한다. 한 회사에 한 명의 설립자가 있는 일대일 대응이기 때문에 두개 다 조건으로 써주어도 무방하다.

<br/>
<br/>
