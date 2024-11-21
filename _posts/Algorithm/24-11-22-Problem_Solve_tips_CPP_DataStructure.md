---
layout: single
title:  "자료구조 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

자료구조관련 stl 사용법 기록

## List
```cpp
#include <list>

// 선언
list<int> l = {1, 2};

// 1을 가리킴
list<int>::iterator t = l.begin();
l.push_front(10);

// t가 가리키는 값 = 1을 출력
cout << *t << '\n'; 

// t가 가리키는 곳 앞에 6을 삽입, 10 6 1 2 5
l.insert(t, 6); 

// 한 칸 뒤로... 2를 가리킴
t++

// t가 가리키는 값을 제거
l.erase(t);
cout << *t << '\n'; // 5
```


## Stack
```cpp
#include <stack>

// 선언
stack<int> s;
// push
s.push(10);
s.push(20);
s.push(30);

// size 확인
cout << S.size() << '\n'; // 3

// 비었는지?
if(S.empty()) cout << "S is empty\n";

// 가장 위에 있는거 
// 값 아무것도 없는데 호출하면 런타임에러 발생
cout << S.top() << '\n'; // 30

// pop
S.pop(); // 30
```

## Queue
```cpp
#include <queue>

queue<int> q;
// push
q.push(10);

// size
q.size()

// 비었나?
q.empty()

// pop
int n = q.pop()

// 가리키는 앞의 값
q.front()

// 가리키는 뒤의 값
q.back();
```

## Deque
```cpp
#include <deque>

deque<int> dq;
// push front
dq.push_front(10); // 10

// push back
dq.push_back(20); // 10 20

// 비었나?
dq.empty()

// pop front
int n = q.pop_front() // 20

// pop front
int n = q.pop_back() // []

dq.push_front(10); // 10
dq.push_back(20); // 10 20

// 앞 원소 확인
dq.front(); // 10
// 뒤 원소 확인
dq.back(); // 20

// insert
dq.insert(dq.begin() + 1, 33); // 10 33 20

// erase
dq.erase(dq.begin() + 2) // 10 33

// clear
dq.clear();
```