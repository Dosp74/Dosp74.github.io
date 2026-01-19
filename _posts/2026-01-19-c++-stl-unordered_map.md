---
title: "[C++] STL unordered_map"
date: 2026-01-19 14:21:00 +0900
last_mod: 2026-01-19
categories: [C++]
tags: [C++, STL, unordered_map, 해시 테이블]
---

C++ STL의 unordered_map은 **Key-Value 형태의 데이터를 해시 기반으로 저장하는 연관 컨테이너** 이다.
이전 포스팅에서 살펴본 `map`과 기능적으로는 유사하지만, **정렬을 하지 않고 속도에 집중한 컨테이너** 라는 점이 가장 큰 차이이다.

`std::unordered_map`은 내부적으로 **해시 테이블** 을 사용한다.

### 특징

- Key 중복 X
- 정렬 X
- 삽입/삭제/탐색: O(1)
- 최악의 경우: O(n) (해시 충돌이 심한 경우)

속도는 빠르지만, 순서가 필요하면 사용하면 안 된다.

### unordered_map의 헤더와 선언

```cpp
#include <unordered_map>

unordered_map<int, string> um;
```

### 주요 멤버 함수

| 함수명            | 설명                              |
| ---------------- | -------------------------------- |
| insert({k, v})   | (k, v) 쌍을 삽입                  |
| um[k]            | k에 해당하는 value 접근            |
| find(k)          | key k를 가리키는 iterator 반환     |
| erase(k)         | key k에 해당하는 원소 삭제         |
| size()           | 저장된 원소 개수 반환              |
| empty()          | 비어 있는지 확인                  |

`map`과 인터페이스는 거의 동일하다.

### 예시 코드

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<int, string> um;

    um.insert({1, "apple"});
    um.insert({3, "banana"});
    um.insert({2, "orange"});

    for (auto p : um) {
        cout << p.first << ": " << p.second << "\n";
    }

    return 0;
}
```

🔽 출력 :

```plaintext
2: orange
3: banana
1: apple
```

출력 순서는 보장되지 않는다.

### operator[] 사용 시 주의점

unordered_map에서도 operator[]는 존재하지 않는 key를 자동 생성한다.

```cpp
unordered_map<int, int> um;

cout << um[5]; // 0 (자동 삽입)
```

따라서 map과 마찬가지로 단순 조회 목적인 경우에는 find()를 사용하는 것이 좋지만, 이는 map의 크기가 커지는 원인이 되기도 한다.

## unordered_map이 빠른 이유

map은 트리 기반(O(logn))인 반면, unordered_map은 해시 기반(O(1))이다.
Key를 해시 함수에 넣어 바로 버킷 위치를 계산하기 때문에 트리를 타고 내려갈 필요가 없다.

다만 여러 Key가 같은 해시 값을 가질 때 해시 충돌이 발생할 수 있다.
충돌이 많아지면 성능이 저하되며, 최악의 경우 O(n) 시간 복잡도를 보이지만 STL의 기본 해시 함수는 대부분의 상황에서 충분히 안정적이다.

## map과 unordered_map 정리

| 항목        | map                    | unordered_map              |
|------------|------------------------|-----------------------------|
| 내부 구조   | 트리                    | 해시 테이블                  |
| 정렬 여부   | O (Key 기준 자동 정렬)    | X                          |
| 탐색 속도   | O(logn)                 | 평균 O(1)                   |
| 순회 순서   | Key 오름차순으로 보장됨    | 보장되지 않음                |
| 범위 탐색   | 가능                     | 불가능                      |

### unordered_map은 언제 쓰일까?

- 정렬이 전혀 필요 없을 때
- 빠른 삽입/탐색이 중요한 경우
- 빈도 카운트, 존재 여부 체크 등

### 마무리

unordered_map은 속도 최우선 컨테이너이다. 평균 O(1)의 빠른 연산을 보여주며, 순서가 필요하면 map, 속도가 중요하면 unordered_map을 사용하면 된다.
