---
title: "[MySQL][Hacker Rank] Olivander's Inventory"
excerpt: "해커랭크 SQL - Basic Join (Medium)"

categories:
    - SQL
tags:
    - [MySQL, HackerRank, JOIN]

toc: true
toc_sticky: true
toc_label:

date: 2022-03-31
last_motified_at:
---
<br/>
<br/>

# Problem
Harry Potter and his friends are at Ollivander's with Ron, finally replacing Charlie's old broken wand.

Hermione decides the best way to choose is by determining the minimum number of gold galleons needed to buy each non-evil wand of high power and age. Write a query to print the id, age, coins_needed, and power of the wands that Ron's interested in, sorted in order of descending power. If more than one wand has same power, sort the result in order of descending age.
<br/>

**Input Format**

The following tables contain data on the wands in Ollivander's inventory:

- **Wands**: The <mark style='background-color: #E5F0FD'>id</mark> is the id of the wand, <mark style='background-color: #E5F0FD'>code</mark> is the code of the wand, <mark style='background-color: #E5F0FD'>coins_needed</mark> is the total number of gold galleons needed to buy the wand, and <mark style='background-color: #E5F0FD'>power</mark> denotes the quality of the wand (the higher the power, the better the wand is).

- **Wands_Property**: The <mark style='background-color: #E5F0FD'>code</mark> is the code of the wand, <mark style='background-color: #E5F0FD'>age</mark> is the age of the wand, and is_evil denotes whether the wand is good for the dark arts. If the value of <mark style='background-color: #E5F0FD'>is_evil</mark> is 0, it means that the wand is not evil. The mapping between code and age is one-one, meaning that if there are two pairs, (code1, age1) and (code2, age2), then code1 != code2 and age1 != age2.


<br/>

## 문제 설명
높은 power, age의 non-evil인 지팡이를 사기 위해 필요한 최소한의 갈레온 수를 구하는 문제이다.

id, age, coins_needed, power를 출력한다.

power가 큰 순으로, power가 같은 경우는 age가 큰 순으로 정렬한다.

<br/>
<br/>
<br/>

---

# Solution
같은 power, age인 지팡이들 중 non-evil이고 가격이 가장 저렴한(coins_needed가 가장 적은) 지팡이만 출력해야 한다.

1. Wands, Wands_Property 테이블을 code를 기준으로 조인하고 non_evil인 지팡이(is_evil=0)만 선택
2. age와 power가 같을 때의 min(coins_needed) 찾기
    - GROUP BY code, power, age
    - code는 뒤에 join 할 때 필요
    - age는 출력할 때 필요함(나중에 또 조인하지 않아도 됨)
    - code와 age가 일대일 대응이기 때문에 두개 다 써도 상관없음
3. id를 출력해야하므로 원래 Wands 테이블에 inner join 하여 필요한 데이터만 남김


<br/>
<br/>
<br/>

---

# Answer

```sql
SELECT  W.id, M.age, W.coins_needed, W.power
FROM    Wands W
        INNER JOIN
        (SELECT W.code, W.power, P.age, MIN(W.coins_needed) min_coins
        FROM    Wands W JOIN Wands_Property P ON (W.code = P.code)
        WHERE    P.is_evil = 0
        GROUP BY    W.code, W.power, P.age) M
        ON  (W.code = M.code) AND (W.power = M.power) AND (W.coins_needed = M.min_coins)
ORDER BY    W.power DESC, M.age DESC;
```

## 오답

```sql
SELECT  W.id, P.age, W.coins_needed, W.power
FROM    Wands W
        INNER JOIN
        (SELECT code, power, MIN(coins_needed) min_coins
        FROM    Wands
        GROUP BY    code, power) W2
        ON (W.code = W2.code) AND (W.power = W2.power) AND (W.coins_needed = W2.min_coins)
        JOIN
        Wands_Property P ON (W.code = P.code)
WHERE   P.is_evil = 0
ORDER BY    W.power DESC, P.age DESC;
```
결과값은 정답으로 나오지만 오답이라고 생각한 이유 :
- 1번 코드는 non_evil인 데이터만 남긴 후에 min_coins를 찾았고, 2번 코드는 반대로 min_coins를 찾고 non_evil인 데이터를 걸러주었다
- 이 순서에 따라 데이터 손실이 있을 수 있다
- 만약, 2번 코드처럼 min_coins를 먼저 찾았을 때 거기에 non_evil이 아닌 데이터가 포함되어 있다면, 해당 데이터는 결과값에 포함될 수 없다. 즉, 해당 power, age인 지팡이는 결과값에서 삭제되는 것이다
- 반대로 1번 코드처럼 non_evil인 데이터를 먼저 걸러주고 min_coins를 찾는다면, 해당 power, age인 지팡이들 중 최소값은 아니지만 non_evil 중 최소값이 포함될 것이다
- 문제는 단순히 높은 power, age의 지팡이가 아닌, 높은 power, age의 non_evil인 지팡이를 구하는 것이므로 해당 데이터를 살려줄 수 있는 1번 코드가 맞다
- 문제의 예시 데이터에 같은 power, age인 지팡이 중 min_coins인 지팡이가 모두 non_evil이기 때문에 통과는 되지만 잘못된 코드이다


<br/>
<br/>
<br/>
