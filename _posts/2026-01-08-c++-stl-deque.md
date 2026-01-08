---
title: "[C++] STL deque"
date: 2026-01-08 18:20:00 +0900
last_mod: 2026-01-08
categories: [C++]
tags: [C++, STL, deque, 백준, 2346번]
---

C++ STL의 **deque** 은 **양쪽 끝에서 삽입과 삭제가 모두 가능한 컨테이너** 이다.
이름은 **double-ended queue** 의 약자이며, vector와 queue의 특징을 적절히 섞은 자료구조라고 볼 수 있다.

---

**std::deque** 은 **앞(front)과 뒤(back) 양쪽에서 모두 O(1) 시간에 삽입/삭제가 가능** 하다.
또한 `vector`처럼 인덱스를 활용한 **임의 접근(random access)** 도 지원한다.

### 특징 요약

- 앞/뒤 삽입, 삭제 -> 빠름
- 인덱스 접근 -> 가능

## 내부 구조

deque은 단일 연속 메모리 블록이 아닌, 여러 개의 작은 블록을 관리하는 구조로 구현되어 있다. 이 덕분에 양쪽 끝에서의 연산이 효율적이지만, 메모리 구조는 vector보다 다소 복잡하다.

시간 복잡도를 정리하면 다음과 같다.

- 앞/뒤 삽입, 삭제: O(1)
- 임의 접근: O(1)
- 중간 삽입/삭제: O(n)

## 헤더 및 선언

```cpp
#include <deque>
using namespace std;

deque<int> dq;
```

## 주요 멤버 함수

| 함수명                | 설명              |
| -------------------- | ---------------- |
| push_back(val)       | 뒤에 요소 추가     |
| push_front(val)      | 앞에 요소 추가     |
| pop_back()           | 뒤 요소 제거       |
| pop_front()          | 앞 요소 제거       |
| front() / back()     | 앞 / 뒤 요소 반환  |
| size()               | 요소 개수 반환     |
| empty()              | 비어 있는지 확인   |
| operator[]           | 인덱스 기반 접근   |

## 예시 코드

```cpp
#include <iostream>
#include <deque>
using namespace std;

int main() {
    deque<int> dq;

    dq.push_back(1);
    dq.push_back(2);
    dq.push_front(10);

    cout << "front: " << dq.front() << "\n";
    cout << "back: " << dq.back() << "\n";

    dq.pop_front();

    for (int i = 0; i < dq.size(); i++) {
        cout << dq[i] << " ";
    }

    return 0;
}
```

🔽 출력 :

```plaintext
front: 10
back: 2
1 2
```

## 실전

![Image](/assets/images/2026-01-08/2026-01-08-baekjoon-2346.png)

[https://www.acmicpc.net/problem/2346](https://www.acmicpc.net/problem/2346)

이 문제는 양쪽 회전이 가능한 자료구조가 필요하다.
앞가 뒤에서 모두 원소를 꺼내고 다시 넣어야 하므로, deque이 가장 적합하다.

```cpp
#include <iostream>
#include <deque>
using namespace std;

int main() {
    int N;
    cin >> N;

    deque<pair<int, int>> dq;
    for (int i = 1; i <= N; i++) {
        int number;
        cin >> number;
        dq.push_back({number, i});
    }

    bool frontTurn = true;

    while (dq.size() != 1) {
        int number;

        if (frontTurn) {
            auto p = dq.front();
            number = p.first;
            cout << p.second << " ";
            dq.pop_front();
        }
        else {
            auto p = dq.back();
            number = p.first;
            cout << p.second << " ";
            dq.pop_back();
        }

        if (number > 0) {
            for (int i = 1; i < number; i++) {
                dq.push_back(dq.front());
                dq.pop_front();
            }

            frontTurn = true;
        }
        else {
            for (int i = -1; i > number; i--) {
                dq.push_front(dq.back());
                dq.pop_back();
            }

            frontTurn = false;
        }
    }

    cout << dq.front().second;

    return 0;
}
```

deque은 양방향 queue + 배열 접근이 필요할 때 쓰는 컨테이너라고 기억하면 편하다.
