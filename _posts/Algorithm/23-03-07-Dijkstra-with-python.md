---
layout: single
title:  "Python으로 구현하는 다익스트라"
category: Algorithm
tags: [Algorithm, Python, Dijkstra]
date: 2023-03-07
comments : true
---

## 다익스트라 알고리즘이란?
특정한 노드에서 다른 노드까지 가는 최단거리를 구하는 알고리즘입니다.

## 구현 방법
구현 방법은 2가지가 있는데, 무조건 개선된 것을 사용할 것.

```python
import heapq
def Dijkstra( graph, dist, start):
    q = []
    heapq.heappush(q, (0, start))
    dist[start] = 0
    while q:
        distance, cur = heapq.heappop(q)
        if dist[cur] < distance:
            continue
        for i in graph[cur]:
            cost = distance + i[1]
            if cost < dist[i[0]] :
                dist[i[0]] = cost
                heapq.heappush(q,(cost,i[0]))
```
* 우선순위 큐를 사용해서 항상 가장 가까운 노드가 나올 수 있도록 한다.
* 시작 노드는 본인과 본인과의 거리기 때문에 0을 넣어준다. 이 때 소팅이 될 수 있도록 첫 항목에 거리값을 넣어준다.
* heap에 넣은 값이 나오면, 해당 그래프와 연결된 노드를 전부 돈다.
* 시작지점에서 해당 노드까지의 거리는, 시작 노드~해당 노드 전까지의 거리 + 현재 노드까지의 거리로 계산한다.
* 이 거리값이 현재 지정된 거리값 보다 더 작을 경우 거리를 업데이트 해 준다.

## 주의할 점
* 최단 거리 테이블은 무조건 무한(1e9) 로 초기화 시킬 것