---
layout: single
title:  "자료구조 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## cpp 순열과 조합
### 순열
`next_permutation`
```cpp
// default
bool next_permutation (BidirectionalIterator first, BidirectionalIterator last);
 
// custom
bool next_permutation (BidirectionalIterator first, BidirectionalIterator last, Compare comp);
```
* 반드시 값을 정렬해 놓고 사용해야 한다
* 오름차순으로 순열이 생성된다
* 중복이 있는 원소들은 제외된다.
* 예시
    ```cpp
    #include <algorithm>

    vector<int> v = {1, 2, 3, 4};
    sort(v.begin(), v.end());
    do{
        for(auto &it : v){
            cout << it << " ";
        }
        cout << "\n";
    } while( next_permutation(v.begin(), v.end()));
    ```

### 조합
* N개의 원소 중에 r개를 뽑는 경우의 수
* 방법 
    * 오름차순으로 할려면 `prev_permutation`을 사용한다.
    * 선택하는 만큼의 선택 배열을 만들어준다
    * 그 배열을 `prev_permutation` 을 돌려서 원래 배열을 출력시킨다.
* 예시
    ```cpp
    vector<int> s{ 1, 2, 3, 4 };
    vector<int> temp{ 1, 1, 0, 0 };
    do {
        for (int i = 0; i < s.size(); ++i) {
            if (temp[i] == 1)
            cout << s[i] << ' ';
        }
        cout << endl;
    } while (prev_permutation(temp.begin(), temp.end()));
    ```