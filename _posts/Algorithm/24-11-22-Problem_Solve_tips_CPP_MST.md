---
layout: single
title:  "이진검색트리 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## 크루스칼
### union-find
* 공통 조상을 찾는 `find` 함수와 두 input값의 공통조상으로 묶어주는 `union` 함수로 구성되어 있다.
* 구현 - find
    ```cpp
    int find(int x){
        if (p[x] < 0){
            return x;
        }

        return p[x] = find(p[x])
    }
    ```
    * 공통 조상을 찾는 과정에서 지나가는 모든 노드를 부모의 직속 노드로 만들어준다.

* 구현 - union
    ```cpp
    bool uni(int x, int y){
        x = find(x); y = find(y);
        if (x == y) return false;
        if(p[x] > p[y]) swap(x, y);
        if (p[x] == p[y]) p[x]--;
        p[y] = x;
        return true
    }
    ```
    * 공통조상이 같으면 같은 무리에 속하므로 false return
    * 두 값이 같을 경우, 한 친구를 부모로 만들어줌 (`p[x]--;`)
    * 부모 자식간을 구성함

### union-find를 활용한 크루스칼 알고리즘
* 가중치가 가장 낮은 순서대로 정렬한다
* 같은 조상을 가지고 있으면 mst로 구성하지 않는다.
* 같은 조상을 가지고 있을 경우, mst에 포함 시킨다.
* `edge count - 1` 만큼의 link 수가 형성이 됐을 경우, mst 생성이 완료된 것으로 판단하고 수행을 종료한다
    ```cpp
    vector<pair<int, int, int>> v;
    sort(v.begin(), v.end());
    int edgeCnt = 0;
    for(auto &it : v){
        int s, e, c;
        tie(c, s, e) = it;
        if (uni(s, e) == false) continue;
        edgeCnt++;
        if(edgeCnt == v - 1) break;
    }
    ```    


## 프림
### 로직
* 임의의 정점을 선택해서 트리에 추가하여, 연결된 간선을 우선순위큐에 추가한다
* 연결 노드가 방문된적 있는 경우 mst를 생성하지 않고, 다음 노드가 방문된 적이 없으면 mst로 추가한 후, 연결 간선을 pq에 추가한다.
* vertex - 1개의 간선을 가질 때까지 계속 반복한다.
### 구현
```cpp
#define X first
#define Y second

int v, e;
vector<pair<int,int>> adj[10005]; 
bool chk[10005];

int cnt = 0; 
priority_queue< tuple<int,int,int>,
                vector<tuple<int,int,int>>,
                greater<tuple<int,int,int>>> pq;
            
chk[0] = 1;
for(auto &nxt : adj[1]){
    pq.push({nxt.X, 1, nxt.Y})
}

while(cnt < v-1){
    int cost, from, to;
    tie(cost, from, to) = pq.top(); pq.pop();
    if (chk[to] == 1) continue;
    chk[to] = 1;
    for(auto &it : adj[to]){
        if( 0 == it.Y ){
            pq.push_back(it.X, to, it.Y);
        }
    }
}
```