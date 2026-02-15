---
title: "[알고리즘] 이분 탐색(Binary Search)과 매개변수 탐색(Parametric Search)"
date: 2026-02-15 22:14:00 +0900
last_mod: 2026-02-15
categories: [알고리즘]
tags: [이분 탐색, 매개변수 탐색, 백준, 1654번]
---

이분 탐색(Binary Search)은 탐색 공간을 절반씩 줄여가며 원하는 값을 찾는 알고리즘이다. **정렬된 배열에서 값을 찾는 알고리즘** 이라고 알고 있지만, 실제로 코딩 테스트에서 더 중요한 형태는 `매개변수 탐색(Parametric Search)`이다.

즉, 단순 배열 탐색이 아닌, **결정 문제(Decision Problem)** 를 반복적으로 풀어가며 최적해를 찾아야 하는 경우가 많다.

## 이분 탐색(Binary Search)

정렬된 배열에서 특정 값을 찾는 기본적인 방식이다.

- 탐색 범위를 left ~ right로 설정
- mid를 기준으로 탐색 범위를 절반으로 줄임
- 시간 복잡도: O(log n)

이러한 형태의 알고리즘이 우리가 많이 아는 이분 탐색이라는 알고리즘인데, 배열이 아닌 값의 범위를 탐색해야 하는 문제로 발전시켜야 한다.

## 매개변수 탐색(Parametric Search)

매개변수 탐색은 다음과 같은 구조를 가진다.

1. 어떤 값 `x`가 주어졌을 때
2. 조건을 만족하는지 판별하는 함수 `f(x)`가 존재하고
3. 그 판별 결과가 **단조(monotonic)** 일 때
4. 이분 탐색으로 최적의 x를 찾는다.

즉,

```plaintext
가능 가능 가능 가능 불가능 불가능 불가능
```

이런 구조를 찾는 것이 핵심이다.

## 대표 예시

![Image](/assets/images/2026-02-15/2026-02-15-baekjoon-1654.png)

[https://www.acmicpc.net/problem/1654](https://www.acmicpc.net/problem/1654)

### 문제 요약

K개의 랜선을 길이 L로 잘라 N개 이상 만들 수 있을 때, 가능한 L의 최댓값을 구하는 문제이다.

랜선 길이를 L이라고 하면,

```plaintext
f(L) = sum(LANs[i] / L)
```

L이 커질수록 만들 수 있는 개수는 줄어든다.

즉, L이 커지면 f(L)은 줄어드는 단조 감소 함수의 형태이다. 따라서 조건 `f(L) >= N을 만족하는 최대 L`을 찾으면 된다.

어떤 L에서 조건을 만족하면 그보다 작은 L은 항상 만족하고, 어떤 L에서 만족하지 않으면 그보다 큰 L도 항상 만족하지 않는다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int K, N;
    cin >> K >> N;
    
    vector<long long> LANs(K);
    long long maxLength = 0;
    
    for (int i = 0; i < K; i++) {
        cin >> LANs[i];
        maxLength = max(maxLength, LANs[i]);
    }
    
    long long left = 1, right = maxLength;
    long long answer = 0;
    
    while (left <= right) {
        long long mid = (left + right) / 2;
        long long cnt = 0;
        
        for (long long l : LANs) {
            cnt += l / mid;
        }
        
        if (cnt >= N) {
            answer = mid;
            left = mid + 1;
        }
        else {
            right = mid - 1;
        }
    }
    
    cout << answer;
    
    return 0;
}
```

왜 정렬 없이 이분 탐색이 가능할까? 우리는 배열을 탐색하는 것이 아니라 랜선의 길이라는 값의 범위(1 ~ maxLength)를 탐색하고 있다.

또한 판별 함수 f(L)이 단조의 형태를 띄고 있기 때문에 mid 기준으로 탐색 범위를 안전하게 버릴 수 있다.

핵심은 문제 해결의 흐름에서 해를 구하는 과정이 단조인지 아닌지에 달려 있다.

이분 탐색은 단순한 알고리즘 문제 풀이에 사용하는 것을 넘어서, 서버 자원 할당 최적화, 네트워크의 대역폭 분배, 최대 처리량 산정과 같은 실무에도 연결하여 적용될 수 있다.

나열한 예시들은 전부 어떤 설정 값 x가 주어졌을 때 조건을 만족하는지를 검사하여 빠르게 최적해를 찾을 수 있는 구조를 지니고 있다.

최대 동시 접속자 수를 만족하는 최소 서버의 수, 지연 시간을 넘지 않는 최대 요청량, 일정 시간 내 처리 가능한 최소 인스턴스 수 등의 다양한 문제를 결정 문제와 단조 구조로 환원하면 해결할 수 있다. 물론, 대략적인 해를 구하는 데 이용하고 현실에서 발생할 수 있는 여러 가지 변수들 또한 고려해야 할 것이다.
