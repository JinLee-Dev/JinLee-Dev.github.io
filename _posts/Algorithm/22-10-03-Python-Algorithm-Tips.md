---
layout: single
title:  "Python으로 알고리즘 풀 때 팁!"
category: Algorithm
tags: [Algorithm, Python]
date: 2022-10-03
comments : true
---

요즘, 파이썬이 알고리즘을 풀기에 편하다는 말을 들어 C++이 아닌 파이썬으로 알고리즘 문제를 풀고 있습니다.
전반적으로 파이썬이 확실히 알고리즘 문제를 풀때 편한 것 같습니다.

하지만 편한 만큼 지원하는 기능이 많기도 해서 헷갈리기도 하고 또 C++과는 달리 속도가 느려 주의해야 할 내용도 있습니다.

이에 대해 정리해 보려 합니다.

---
# 꿀팁
## Input 쉽게 넣는 법
띄어쓰기로 구분된 int형 데이터 넣는 방법
```python
node, link, start = map(int, sys.stdin.readline().split(' '))
```
* <code>split()</code> : 함수 괄호 내 문자열을 기준으로 나눔.
* <code>map()</code> : Split된 여러 input에 대해 일괄적으로 형변환을 함

<br>

# 주의해야 할 점
## Input 함수
* <code>input()</code>말고 <code>sys.stdin.readline()</code> 를 쓸 것. 후자가 더 빠름

## Python Queue는 Deque로 사용할 것
* Queue는 멀티쓰레드 환경을 지원하고, Deque는 지원하지 않기 때문에 후자가 더 빠르다고 함.

## 재귀할 때 Recursion Error 조심하기
* 구현 중에 재귀를 쓸 때 아래 코드를 꼭 넣어줄 것
```python
sys.setrecursionlimit(10 ** 6)
```

## 파이썬으로 무한대의 값을 표현하기
* Python은 e나 E를 이용해서 지수를 표현할 수 있음.
```python
# 10의 9제곱
1e9
```
* 만약 무한으로 사용할 수 있는 값이 10억 이하라면, 최단거리의 무한대의 거리를 1e9로 이용할 수 있다.

## Custom Sort function
cmp_to_key를 이용해서 custom sorting 가능

```python
from functools import cmp_to_key
def comparator_by_number(number1, number2):
    if (number1 + number2) > (number2 + number1):
        return -1
    elif (number1 + number2) < (number2 + number1):
        return 1
    else:
        return 0
v.sort(key=cmp_to_key(comparator_by_number))
```

## String을 List로 나누기
간단히 형변환만 하면 됨
```python
str="python"
# ['p', 'y', 't', 'h', 'o', 'n']
lstr = list(str)
```

## 문자열 나누기와 결합
* 문자열 나누기 : split("나눌 기준 문자")
    * 공백 기준으로 
    ```python
    str="py thon"
    # ['py', 'thon']
    lstr = str.split()
    ```

    * 문자 기준으로
        ```python
        str="python"
        # ['pyth', 'n']
        lstr = str.split("o")
        ```

* 문자열 합치기 : join
    * 공백 더해서 합치기
    ```python
    l = ['pyth', 'on']
    # lstr = ['pyth on']
    lstr = " ".join(l)
    ```
    * 딕셔너리
    ```python
    l = {'key1':'py', 'key2':'thon'}
    # lstr = ['key1 key2']
    lstr = " ".join(l)

    # lstr = ['key1 key2']
    lstr = " ".join(l.keys())

    # lstr = ['py thon']
    lstr = " ".join(l.values())
    ```
    