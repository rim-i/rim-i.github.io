---
title: "[Programmers][Python] 없는 숫자 더하기(Lv.1)"
excerpt: "프로그래머스 코딩테스트 by Python"

categories:
    - algorithm
tags:
    - [Python]

toc: true
toc_sticky: true
toc_label:

date: 2021-11-17
last_motified_at:
---

# 1. 문제

## 문제 설명
0부터 9까지의 숫자 중 일부가 들어있는 배열 numbers가 매개변수로 주어집니다. numbers에서 찾을 수 없는 0부터 9까지의 숫자를 모두 찾아 더한 수를 return 하도록 solution 함수를 완성해주세요.

## 제한 사항
- 1 ≤ numbers의 길이 ≤ 9
- 0 ≤ numbers의 모든 수 ≤ 9
- numbers의 모든 수는 서로 다릅니다.

# 2. 풀이
없는 숫자를 찾아서 더하는 것보다 숫자의 범위가 0~9로 정해져있으므로 0부터 9까지 더한 수인 45에서 배열에 있는 수를 빼는 것이 더 간단하다.
- 0+1+…+9는 sum(range(10))로 구할 수 있지만 매번 새로 구할 필요가 없으므로 함수의 효율화를 위해 45로 써주는 것이 더 좋다
- 리스트에 있는 수를 더하는 것은 for문을 사용해서도 가능하지만 간단하게 sum함수를 사용할 수 있다

## 코드1
```py
def solution(numbers):
    answer = sum(range(10))
    for num in numbers:
        answer -= num
    return answer
```


## 코드2
```py
def solution(numbers):
    answer = 45 - sum(numbers)
    return answer
```
시간효율을 위해 45로 고정해두고 for문 대신 sum 함수를 사용하였다.

[Code](https://github.com/rim-i/algorithms/blob/main/%EC%97%86%EB%8A%94%20%EC%88%AB%EC%9E%90%20%EB%8D%94%ED%95%98%EA%B8%B0.ipynb)