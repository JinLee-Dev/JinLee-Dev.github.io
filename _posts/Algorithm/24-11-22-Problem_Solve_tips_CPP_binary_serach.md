---
layout: single
title:  "이분탐색 - CPP"
category: Algorithm
tags: [Algorithm, CPP]
date: 2024-11-22
comments : true
---

## 이분탐색을 반으로 잘 나누는 방법
* 타겟보다 값이 작을 경우, 시작 지점을 중간 지점으로 옮긴다
* 타겟보다 값이 클 경우, 끝 지점을 중간 지점으로 옮긴다.
* 처음 나눌 때 mid를 기준으로 완전히 균등하게 나눠지거나, start쪽으로 값을 치우치게 하는 것이 좋다.
* 한번 직접 나눈 다음에 결정해보기

## stl로 구현하는 이분탐색
* 함수 : `binary_search(begin, end, value)`
* 예시
    ```cpp
    sort(sgV.begin(), sgV.end());

	for (auto v : scV) {
		cout << binary_search(sgV.begin(), sgV.end(), v) << '\n';
	}
    ```

## upper_bound와 lower_bound
* 특정 인덱스가 있는 시작 부분과, 끝 부분을 알 수 있는 함수
    ```cpp
    sort(sgV.begin(), sgV.end());

	for (auto v : scV) {
		cout << upper_bound(sgV.begin(), sgV.end(), v) - lower_bound(sgV.begin(), sgV.end(), v) << '\n';
	}
    ```

## upper_bound, lower_bound를 직접 구현하는 방법
* 전부 다 끝 부분을 생각할 것
* `upper_bound`는 가장 마지막 인덱스를 포함해야 함. 타겟보다 값이 클 경우 end값을 업데이트 할 것
* `lower_bound`는 가장 첫 인덱스를 포함해야 함. 타겟보다 값이 같거나 클 경우 end값을 업데이트 할 것