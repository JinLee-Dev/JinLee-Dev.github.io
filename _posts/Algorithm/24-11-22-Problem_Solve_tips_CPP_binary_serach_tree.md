---
layout: single
title:  "이진검색트리 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## set
* 트리 형식의 stl 자료구조
* 처음부터 sorting 되어 있는 순서대로 들어옴
* 값이 중복될 수 없다.
* `begin`은 start지점, `end`는 end + 1지점을 반환한다
    * end 참조할 땐 아래와 같이 참조해볼 것.
    ```cpp
    for (int i = 100; i >= 0; i--) {
          if (probByLevel[i].empty()) continue;
          cout << *(prev(probByLevel[i].end())) << '\n';
          break;
    }
    ```
* insert, erase, find 전부 `log(n)`이다
    ```cpp
    std::set<int> s = {3, 1, 4, 1, 5};
    ```
* 애초에 sorting이 되어 있기 때문에, 아래와 같은 코드가 가능하다
    ```cpp
    auto it3 = s.lower_bound(-20);
    for(; it3 != s.end(); ++it) {
        cout << *it3 << '\n';
    }
    ```


## multiset
* 중복을 허용하는 stl 자료구조
* `upper_bound` 및 `lower_bound` 사용이 가능하다.
