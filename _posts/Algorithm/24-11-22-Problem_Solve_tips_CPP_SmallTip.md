---
layout: single
title:  "CPP로 알고리즘 풀기 미세팁"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## 미세팁들
### Pair / tuple값을 한번에 받아오는 방법
```cpp
tie(cost, a, b) = edge[i]
```

### 값을 바꾸는 방법
```cpp
swap(a, b);
```

### 값을 한번에 초기화 하는 방법 - memset 대용
```cpp
vector<int> l;
fill(l.begin(), l.end(), -1);
```

## 한번에 출력하기
```cpp
std::copy(&dp[1], &dp[1] + cnt, std::ostream_iterator<int>(std::cout, " "));
std::cout << std::endl;
```