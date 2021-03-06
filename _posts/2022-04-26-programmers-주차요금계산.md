---
title: "[Programmers][Python] 주차 요금 계산(Lv.2)"
excerpt: "프로그래머스 코딩테스트 - 2022 KAKAO BLIND RECRUITMENT"

categories:
    - algorithm
tags:
    - [Python, Programmers, Sort]

toc: true
toc_sticky: true
toc_label:

date: 2022-04-26
last_motified_at:
---
<br/>
<br/>

# 1. Problem

00:00부터 23:59까지의 입/출차 내역을 바탕으로 차량별 누적 주차 시간을 계산하여 요금을 일괄로 정산

- 기본 시간 이하면, 기본 요금 청구
- 기본 시간 초과하면 기본 요금 + 단위시간 * 단위 요금
- 이용 시간은 올림하여 단위 시간과 비교
- 차량 번호가 작은 자동차부터 ****청구할 주차 요금을 차례대로 정수 배열에 담아서 return
- 동일한 차량이 두번 이용할 수 있음

<br/>

**[input]**

- **fees** : **[기본 시간, 기본 요금, 단위 시간, 단위 요금]** 의 배열 (단위:분,원)
- **records** : “시각 차량번호 내역(IN/OUT)” 형식의 배열. 하루 동안의 입/출차 기록
    - **시각** : HH:MM형식. 00:00 ~ 23:59
    - **차량번호** : 길이 4의 문자열
    - **내역** : IN은 입차, OUT은 출차
    - 입차된 후에 출차된 내역이 없다면, 23:59에 출차된 것으로 간주

<br/>

<details>
<summary>문제 원본</summary>
<div markdown="1">       

**문제 설명**

주차장의 요금표와 차량이 들어오고(입차) 나간(출차) 기록이 주어졌을 때, 차량별로 주차 요금을 계산하려고 합니다. 아래는 하나의 예시를 나타냅니다.

- **요금표**

    |기본 시간(분)|기본 요금(원)|단위 시간(분)|단위 요금(원)|
    |---|---|---|---|
    |180|5000|10|600|

- **입/출차 기록**

    |시각(시:분)|차량 번호|내역|
    |---|---|---|---|
    |05:34|5961|입차|
    |06:00|0000|입차|
    |06:34|0000|출차|
    |07:59|5961|출차|
    |07:59|0148|입차|
    |18:59|0000|입차|
    |19:09|0148|출차|
    |22:59|5961|입차|
    |23:00|5961|출차|

- **자동차별 주차 요금**

    |차량 번호|누적 주차 시간(분)|주차 요금(원)|
    |---|---|---|
    |0000|34 + 300 = 334|5000 + ⌈(334 - 180) / 10⌉ x 600 = 14600|
    |0148|670|5000 +⌈(670 - 180) / 10⌉x 600 = 34400|
    |5961|145 + 1 = 146|5000|

    - 어떤 차량이 입차된 후에 출차된 내역이 없다면, 23:59에 출차된 것으로 간주합니다.
        - 0000번 차량은 18:59에 입차된 이후, 출차된 내역이 없습니다. 따라서, 23:59에 출차된 것으로 간주합니다.
    - 00:00부터 23:59까지의 입/출차 내역을 바탕으로 차량별 누적 주차 시간을 계산하여 요금을 일괄로 정산합니다.
    - 누적 주차 시간이 기본 시간이하라면, 기본 요금을 청구합니다.
    - 누적 주차 시간이 기본 시간을 초과하면, 기본 요금에 더해서, 초과한 시간에 대해서 단위 시간 마다 단위 요금을 청구합니다.
        - 초과한 시간이 단위 시간으로 나누어 떨어지지 않으면, 올림합니다.
        - ⌈a⌉ : a보다 작지 않은 최소의 정수를 의미합니다. 즉, 올림을 의미합니다.
    
    주차 요금을 나타내는 정수 배열 fees, 자동차의 입/출차 내역을 나타내는 문자열 배열 records가 매개변수로 주어집니다. 차량 번호가 작은 자동차부터 청구할 주차 요금을 차례대로 정수 배열에 담아서 return 하도록 solution 함수를 완성해주세요.

<br/>
<br/>

**제한사항**

- `fees`의 길이 = 4
    - fees[0] = `기본 시간(분)`
    - 1 ≤ fees[0] ≤ 1,439
    - fees[1] = `기본 요금(원)`
    - 0 ≤ fees[1] ≤ 100,000
    - fees[2] = `단위 시간(분)`
    - 1 ≤ fees[2] ≤ 1,439
    - fees[3] = `단위 요금(원)`
    - 1 ≤ fees[3] ≤ 10,000
- 1 ≤ `records`의 길이 ≤ 1,000
    - `records`의 각 원소는 `"시각 차량번호 내역"` 형식의 문자열입니다.
    - `시각`, `차량번호`, `내역`은 하나의 공백으로 구분되어 있습니다.
    - `시각`은 차량이 입차되거나 출차된 시각을 나타내며, `HH:MM` 형식의 길이 5인 문자열입니다.
        - `HH:MM`은 00:00부터 23:59까지 주어집니다.
        - 잘못된 시각("25:22", "09:65" 등)은 입력으로 주어지지 않습니다.
    - `차량번호`는 자동차를 구분하기 위한, `0'~'9'로 구성된 길이 4인 문자열입니다.
    - `내역`은 길이 2 또는 3인 문자열로, `IN` 또는 `OUT`입니다. `IN`은 입차를, `OUT`은 출차를 의미합니다.
    - `records`의 원소들은 시각을 기준으로 오름차순으로 정렬되어 주어집니다.
    - `records`는 하루 동안의 입/출차된 기록만 담고 있으며, 입차된 차량이 다음날 출차되는 경우는 입력으로 주어지지 않습니다.
    - 같은 시각에, 같은 차량번호의 내역이 2번 이상 나타내지 않습니다.
    - 마지막 시각(23:59)에 입차되는 경우는 입력으로 주어지지 않습니다.
    - 아래의 예를 포함하여, 잘못된 입력은 주어지지 않습니다.
        - 주차장에 없는 차량이 출차되는 경우
        - 주차장에 이미 있는 차량(차량번호가 같은 차량)이 다시 입차되는 경우

</div>
</details>


<br/>
<br/>
<br/>


# 2. Solution
1. <mark style='background-color: #E5F0FD'>records 배열의 시각, 차량번호, 내역 문자열 분리</mark> → 2차원 리스트
    - 다음 단계에서 주차 시간을 계산하기 위해 시간:분 형식에서 분 단위 시간으로 변경
    - <mark style='background-color: #E5F0FD'>차량번호, 시간 순으로 정렬</mark>
    
    <br/>

2. 누적 주차 시간 구하기
    - 같은 차량이 여러번 이용 가능하므로 <mark style='background-color: #E5F0FD'>차량번호 순으로 정렬된 2차원 리스트</mark>를 활용함
        - 처음엔 {차량번호:시각}으로 구성된 IN, out 딕셔너리를 각각 만들어야겠다 생각했으나, 중복 이용 차량을 따로 저장할 수 없는 문제
    - IN, OUT에 대한 정보가 각각 리스트의 다른 원소로 저장되어 있고, 이용 시간을 구하기 위해서는 두 정보를 한번에 가져와야 하므로 <mark style='background-color: #E5F0FD'>for문이 아닌 while문을 사용</mark>
        - i를 인덱스 변수로 사용
        - IN, OUT에 대한 정보가 모두 있는 경우는 +2, in만 있는 경우는 +1
    - <mark style='background-color: #E5F0FD'>인덱스 i의 차량 번호와 다음 인덱스의 차량 번호가 같다면 IN, OUT의 한 세트</mark> → 각 인덱스에서 입고 시각, 출고 시각 가져옴

        <mark style='background-color: #E5F0FD'>같지 않다면 OUT 기록 없음</mark> → 입고 시각만 가져오고 출고 시각은 23:59
        - 주차장에 이미 있는 차량(차량번호가 같은 차량)이 다시 입차되는 경우는 없다고 했으므로 IN, OUT을 따로 판단할 필요 x
        - 즉, OUT된 기록 없이 같은 차량이 다시 IN 하지 않는다는 말이므로 OUT기록이 없는 것은 마지막 IN에 대해서만 발생
    - <mark style='background-color: #E5F0FD'>마지막 인덱스가 IN인 경우</mark> 다음 인덱스를 가져와 차량번호가 같은지 판단하는 조건문에서 <mark style='background-color: #E5F0FD'>인덱스 에러</mark>가 발생함 → 먼저 마지막 인덱스인지 조건문으로 확인
    - <mark style='background-color: #E5F0FD'>누적 주차 시간 계산</mark>
        - `딕셔너리.get(x, 'default value')`
            - x라는 Key에 대응되는 Value를 돌려줌
            - 해당 key가 존재하지 않는 경우, `딕셔너리[x]` 는 오류를 발생시키지만 get함수는 None을 돌려줌
            - key 값이 없을 경우 미리 정해 둔 디폴트 값을 가져오게 할 수 있음
    
    <br/>

3. 주차 요금 계산
    - 기본 시간 이하인 경우 : 기본 요금
    - 기본 시간 초과한 경우 : 기본 요금 + 올림((이용 시간 - 기본 시간) / 단위 시간) * 단위 요금
        - math 라이브러리에 `ceil(x)` 함수


<br/>
<br/>
<br/>



## Code
```py
import math
def solution(fees, records):
    answer = {}
    
    # [시각, 차량번호, 입출고]로 구성된 2차리스트 / 차량번호, 시간 순으로 정렬 / 시간은 분단위로 변경
    recordsList = []
    for rec in records:
        time, num, io = rec.split()
        hh, mm = time.split(':')
        recordsList.append([(int(hh) * 60 + int(mm)), num, io])
    recordsList = sorted(recordsList, key=lambda x:(x[1],x[0]))
    
    # 누적 주차 시간
    i = 0
    while i < len(recordsList):
        num = recordsList[i][1]
        if i == len(recordsList) - 1:
            in_time, out_time = recordsList[i][0], (23 * 60 + 59)
            i += 1
        elif num == recordsList[i+1][1]:
            in_time, out_time = recordsList[i][0], recordsList[i+1][0]
            i += 2
        else:
            in_time, out_time = recordsList[i][0], (23 * 60 + 59)
            i += 1
        diff = out_time - in_time
        answer[num] = answer.get(num, 0) + diff
    
    # 주차 요금 계산
    for num, cum_time in answer.items():
        if cum_time <= fees[0]:
            answer[num] = fees[1]
        else:
            answer[num] = fees[1] + math.ceil((cum_time - fees[0]) / fees[2]) * fees[3] 
    
    return [value for key, value in sorted(answer.items())]
```

<br/>
<br/>

## Sort

a라는 객체를 정렬할 때

```py
a.sort()  # 해당 리스트에서 정렬
a = sorted(a)  # 정렬된 리스트 반환
```
- list, tuple, dictionary, str 모두 사용 가능
- `key` : 정렬 기준
    - 정렬 기준이 복수 개일 경우, 튜플로 묶어서 사용
    - `-` 를 붙이면, 현재 정렬차순과 반대
- `reverse` : True면 내림차순 (default = False)

<br/>
<br/>

**List**

2차원 리스트에서 특정 값 기준으로 정렬하고 싶은 경우

```py
arr.sort(key=lambda x:x[0])  # 첫번째 값 기준
arr.sort(key=lambda x:x[1])  # 두번째 값 기준
arr.sort(key=lambda x:(x[0], -x[2]))  # 첫번째 값 기준으로 오름차순 정렬, 세번째 값 기준으로 내림차순 정렬
```

<br/>
<br/>

**Dictionary**
1. key 기준 정렬

    ```py
    sorted(d)          # key 리스트 반환
    sorted(d.items())  # (key,value) 리스트 반환
    dict(sorted(d.items()))  # 딕셔너리 반환
    ```
    
    <br/>

2. value 기준 정렬

    ```py
    sorted(d, key=lambda x:d[x])   # key 리스트 반환
    sorted(d.items(), key=lambda x:x[1])  # (key,value) 리스트 반환
    sorted(d, key=d.get)  # key 리스트 반환
    ```

<br/>
<br/>
<br/>


[<span style='color: #8DB3E1'>사용한 Code</span>](https://github.com/rim-i/algorithms/blob/main/%5BLv.2%5D%20%EC%A3%BC%EC%B0%A8%EC%9A%94%EA%B8%88%EA%B3%84%EC%82%B0.ipynb)

<br/>
<br/>
