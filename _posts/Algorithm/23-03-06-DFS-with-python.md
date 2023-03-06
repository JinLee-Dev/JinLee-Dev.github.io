---
layout: single
title:  "Python으로 구현하는 DFS"
category: Algorithm
tags: [Algorithm, Python, DFS]
date: 2023-03-06
comments : true
---

## DFS(Depth First Algorithm)란?
가장 깊은 곳을 먼저 탐색하는 완전 탐색 알고리즘
![DFS](/assets/img/375px-Depth-first-tree.png)

## DFS의 알고리즘의 파이썬 구현 방식
DFS의 Python 구현 코드는 아래와 같습니다.
```python
def DFS(graph, v, visited):
    visited[v] = True
    for idx in graph[v]:
        if False == visited[idx]:
            DFS(graph, idx, visited)
```

* 그래프는 `List[List]` 로 구성. 배열로 생성하면 메모리 터지니 주의.
* 재귀를 활용하여 스택에 올려서 찾는 방식

## 주의할 점
* 재귀를 돌리니 반드시 `sys.setrecursionlimit(10 ** 6)` 를 선언해줄 것
* BFS와 마찬가지로 방문 여부 체크해줄 것
* 보통은 BFS 보다 DFS가 느림. 하지만 DFS로만 풀리는 문제들이 있으니 알아두면 좋음