---
layout: single
title:  "Python으로 구현하는 백트래킹"
category: Algorithm
tags: [Algorithm, Python, DFS, BackTracking]
date: 2023-03-08
comments : true
---

## 백트래킹이란?
완전 탐색의 한 종류.
DFS를 돌면서, 모든 경우의 수를 찾아가도록 구현을 하는 것을 의미합니다.
백트래킹의 포인트는 2가지이며, 아래와 같습니다.
* 탐색 후 이전 상황으로 되돌려야 윗 노드에서 그 다음 Case를 확인할 수 있습니다.
* 가지치기
*- 더 이상 유효하지 않은 조건을 잘 쳐내야 소요 시간을 줄일 수 있습니다.

## 구현
* DFS의 응용인데, 대표적인 문제인 N-Queen 구현으로 대체합니다.
```python

# Queen을 놓을 수 없는지?
def CanAttack(depth, queen):
    for i in range(depth):
        if queen[depth] == queen[i] or abs(queen[depth] - queen[i]) == depth - i:
            return True
    return False

def dfs(count, depth, queen):
    answer = 0
    if depth == count:
        return 1

    else:
        for i in range(count):
            # 해당 위치가 Queen을 넣을 수 없는 위치라면 여기서 바로 중지시킨다.
            queen[depth] = i
            if True == CanAttack(depth, queen):
                continue
            else:  
                answer = answer + dfs(count, depth + 1, queen)
    return answer

```

## 주의할 점
* 아직 백트래킹 문제를 많이 풀어보진 않아서, 어떤 것을 주의해야 할지 감이 안오는 상황.
* N-Queen을 풀었을 땐, 2차원 배열로 구현하니 여지없이 바로 시간초과가 났던 것을 보면 시간에 주의해서 풀어야 할 것 같습니다.