---
layout: single
title:  "자료구조 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## BFS 기본 구현
* 너비 우선탐색
* 큐를 사용한다
    ```cpp
    #define X first
    #define Y second // pair에서 first, second를 줄여서 쓰기 위해서 사용
    int dx[4] = {1,0,-1,0};
    int dy[4] = {0,1,0,-1}; // 상하좌우 네 방향을 의미

    queue<pair<int,int>> q;
    // 중괄호를 쓰면 pair가 만들어진다
    Q.push({0,0});
    while(!Q.empty()){
        // 한 줄로 쓰면 참조 + pop을 할 수 있다.
        pair<int,int> cur = Q.front(); Q.pop();
        // 상하좌우 칸을 살펴볼 것이다.
        for(int dir = 0; dir < 4; dir++) { 

            // nx, ny에 dir에서 정한 방향의 인접한 칸의 좌표가 들어감
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir]; 

             // 범위 밖일 경우 넘어감
            if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;

             // 이미 방문한 칸이거나 파란 칸이 아닐 경우
            if(vis[nx][ny] || board[nx][ny] != 1) continue;

            vis[nx][ny] = 1; // (nx, ny)를 방문했다고 명시
            Q.push({nx,ny});
        }
    }
    ```