---
title: "[알고리즘] 최소 신장 트리(Minimum Spanning Tree)"
date: 2025-06-27 21:22:00 +0900
last_mod: 2025-06-27
categories: [알고리즘]
tags: [그리디 알고리즘, 최소 신장 트리]
---

이번 포스팅에서는 **최소 신장 트리(Minimum Spanning Tree)** 에 대해 다루려고 한다.

최소 신장 트리는 **그리디 알고리즘(Greedy Algorithm)** 을 기반으로 동작한다.<br>
그리디 알고리즘이 생소하다면 지난 포스팅을 보고 오면 흐름을 더 잘 이해할 수 있다.<br><br>

👉 [[알고리즘] 그리디 알고리즘(Greedy Algorithm) - 거스름돈 문제로 이해하기](https://dosp74.github.io/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98/greedy-algorithm)<br><br>

## 그리디 알고리즘과 최소 신장 트리

최소 신장 트리를 구할 때 **각 단계에서 가장 이득이 되는 선택을 반복** 한다는 점에서 그리디한 접근(Greedy Approach)을 따른다.

그리디 알고리즘은 일단 1번 선택하면 번복하지 않고, 계속 선택하고 끝이기 때문에 지역적으로 최적인 선택의 집합이 정말 **전체적으로도 최적인지** 는 문제에 따라 다르다.<br><br>
따라서 항상 그 **선택의 정당성** 을 **증명** 해야 한다.<br><br>

일반적인 그리디 알고리즘의 구조는 다음과 같다.

- Selection Procedure (최선의 선택)
- Feasibility Check (잘못된 선택인지 확인)
- Solution Check (해결이 되었는지 확인)<br><br>

이를 의사코드(Pseudocode)로 표현해보면,

```plaintext
GreedyAlgorithm():
    S = ∅
    while (해가 완성되지 않았다면):
        x = 선택 가능한 후보 중 최선의 선택     // Selection Procedure

        if (x가 유효하지 않다면):     // Feasibility Check
            continue     // 다음 후보 고려

        S = S ∪ {x}     // 현재 선택을 해에 포함시킴

        if (S가 전체 해가 되었다면):     // Solution Check
            return S

    return 미완성 또는 해가 없음
```

이러한 알고리즘의 프로세스대로 진행할 때 최적해를 구할 수 있는 문제 중 하나가 바로 최소 신장 트리(Minimum Spanning Tree)이다.

## 최소 신장 트리(Minimum Spanning Tree)란?

최소 신장 트리는 다음과 같은 그래프에서 정의된다.

- **Undirected Graph (edge에 방향이 없는 그래프)**
- **Connected Graph (모든 vertex가 연결된 그래프, 즉 섬이 없는 그래프)**
- **Acyclic Graph (사이클이 없는 그래프, 즉 자기 자신으로 돌아오는 path가 없는 그래프)**

이 세 그래프의 교집합이 바로 **Tree** 이다.<br><br>

그럼, 이제 **신장(Spanning)** 이라는 단어의 뜻이 무엇인지 알아야 할텐데,<br>
신장(Spanning)은 "생성"이라는 뜻을 가지고 있다.<br>

뜻이 모호하다.<br>
대학교 알고리즘 강의에서 나온 용어의 정의를 가져와봤다.<br><br>

Spanning Tree for undirected graph<br>
-> a connected subgraph that contains all the vertices in G and is a tree<br>

Minimum Spanning Tree for undirected graph<br>
-> a spanning tree of G with minimum weight<br><br>

이를 토대로 정리해보면, 최소 신장 트리는 주어진 그래프의 모든 정점을 포함하며, 사이클이 없는 트리 형태의 부분 그래프 중 `엣지 가중치의 합이 최소인 부분 그래프`이다.<br><br>

무슨 말인지 이해가 잘 안 될 것이다.<br>
그림을 통해서 이해해보자.

![Image](/assets/images/2025-06-27/2025-06-27-graph.png)

위와 같은 그래프가 있다.<br>
이 그래프의 Spanning Tree를 구해보자.

![Image](/assets/images/2025-06-27/2025-06-27-subgraph.png)

이 그래프는 위 그래프의 Spanning Tree일까?<br><br>

안타깝게도, 그래프의 아래쪽을 보면 Cycle이 존재하기 때문에 이 그래프는 Spanning Tree가 될 수 없다.<br>
정확하게는 Cycle이 존재하므로 Tree가 될 수 없다.

![Image](/assets/images/2025-06-27/2025-06-27-spanning-tree.png)

그렇다면 이 그래프는 Spanning Tree일까?<br><br>
그렇다. 이 그래프는 주어진 그래프의 모든 정점을 포함하며, 사이클이 없는 트리 형태의 부분 그래프이기 때문에 Spanning Tree이다.<br>

이제 어느 정도 감이 왔을 거라고 생각한다.<br><br>
이러한 Spanning Tree는 그래프에서 여러 개가 존재할 수 있다. 모든 Spanning Tree 중 엣지 가중치의 합이 최소인 Spanning Tree가 바로 최소 신장 트리(Minimum Spanning Tree)이다.<br><br>

그리고 이러한 최소 신장 트리는 바로 그리디 알고리즘으로 쉽게 구할 수 있다.

참고로, 그래프에서 최소 신장 트리는 여러 개가 나올 수 있다. 이 그래프에서 최소 신장 트리는 두 개가 있다.

![Image](/assets/images/2025-06-27/2025-06-27-minimum-spanning-tree-1.png)

<br>

![Image](/assets/images/2025-06-27/2025-06-27-minimum-spanning-tree-2.png)

## 최소 신장 트리를 구하는 방법 - Prim's Algorithm

최소 신장 트리를 구하는 방법으로 가장 널리 알려진 알고리즘은 두 개가 있다.<br>

프림의 알고리즘과 크루스칼의 알고리즘이 있다. (여기서 프림과 크루스칼은 사람의 이름이다.)<br><br>
먼저 프림의 알고리즘에 대해 소개하겠다.<br><br>

### 프림의 알고리즘 아이디어 요약

트리 내 한 정점에서 시작해, 인접한 가장 짧은 edge를 가진 정점을 선택하며 정점을 확장해나간다. 이 과정에서 트리 구조는 유지된다.<br><br>

## 프림 알고리즘의 Pseudocode

```plaintext
F = ∅     // F는 edge의 집합, vertex 개수 - 1 만큼의 edge가 필요
Y = {v₁}     // Y는 vertex의 부분집합

while (문제가 해결되지 않은 경우):
    V - Y에 있는 vertex 중 Y와 가장 가까운 vertex 선택     // V는 모든 vertex의 집합

    해당 vertex를 Y에 추가
    해당 vertex와 Y 사이의 edge를 F에 추가

    if (Y == V):     // 모든 정점이 포함되었으면
        문제 해결 완료
```

프림의 알고리즘은 다음과 같은 절차로 구성된다.

- `Y`: 현재 선택된 vertex들의 집합
- `F`: 최소 신장 트리를 구성할 edge들의 집합

반복적으로 `Y`에 가장 가까운 정점을 선택하며 트리를 확장해간다.

## 프림 알고리즘 구현

그렇다면 이 프림 알고리즘을 실제로 어떻게 구현할까?<br><br>

더 좋은 자료구조가 있을 수 있지만, 이해를 쉽게 하기 위해 배열을 사용해보자.

[C++]

```cpp
#include <iostream>
#include <climits>
using namespace std;

const int V = 5;     // 정점 수

void prim(int W[V][V]) {
    int nearest[V];     // nearest[i] = Y에 있는 V_i와 가장 가까운 정점의 번호
    int distance[V];     // distance[i] = V_i와 nearest[i] 간의 거리(edge weight)
    bool Y[V];     // Y[i] = true이면 트리에 포함된 정점(즉, Y 집합)

    // 1. 초기화
    for (int i = 0; i < V; i++) {
        nearest[i] = 0;     // V_1밖에 없는 상태
        distance[i] = W[0][i];     // W[V_1][i]로 초기화
        Y[i] = false;     // 아직 트리에 포함되지 않음
    }

    Y[0] = true;     // V_1을 시작점으로 선택
    int totalWeight = 0;

    cout << "선택된 간선들(u - v : weight):\n";

    // 5. 모든 정점이 포함될 때까지 반복
    for (int step = 0; step < V - 1; step++) {
        int min = INT_MAX;
        int vnear = -1;

        // 2. distance 배열에서 최솟값을 갖는 정점 선택
        for (int i = 1; i < V; i++) {
            if (!Y[i] && distance[i] < min) {
                min = distance[i];
                vnear = i;
            }
        }

        if (vnear == -1)
            break;

        // 3. 그 정점을 트리에 추가, 간선 출력
        Y[vnear] = true;
        totalWeight += W[vnear][nearest[vnear]];
        cout << "V" << nearest[vnear] + 1 << " - V" << vnear + 1 << " : " << W[vnear][nearest[vnear]] << "\n";

        // 4. 연결되지 않은 정점들에 대해 nearest, distance 갱신
        for (int i = 1; i < V; i++) {
            if (!Y[i] && W[i][vnear] < distance[i]) {
                distance[i] = W[i][vnear];
                nearest[i] = vnear;
            }
        }
    }

    cout << "총 가중치 : " << totalWeight << "\n";
}

int main() {
    int W[V][V] = {
        {0, 1, 3, INT_MAX, INT_MAX},
        {1, 0, INT_MAX, 6, INT_MAX},
        {3, INT_MAX, 0, 4, 2},
        {INT_MAX, 6, 4, 0, 5},
        {INT_MAX, INT_MAX, 2, 5, 0}
    };

    prim(W);

    return 0;
}
```

Y(vertex subset)와 가장 가까운 vertex 찾기를 반복한다.<br><br>
입력으로<br>
W[i][j] (가중치 테이블 - symmetric, W[j][i]와 같다)<br>
nearest[i] (V_i와 가장 가까운 Y에 있는 vertex 번호)<br>
distance[i] (V_i와 nearest[i] 사이의 edge weight, 즉 V_i와 가장 가까운 Y에 있는 vertex 간 거리)<br>
을 입력받는다.<br><br>

distance가 가장 작을 때의 nearest 값을 Y에 반복적으로 넣어야 한다.<br><br>

가장 작은 edge weight을 가진 vertex를 고르기 위해 distance가 필요하며, 고르면 연결된 vertex가 무엇인지 알아야 하므로 nearest가 필요하다.<br><br>

nearest[i]는 V_1밖에 없는 상태에서 출발하므로 0으로 초기화하고, distance[i]는 W[0][i]로 초기화한다.<br><br>

---

`요약`<br>

1. 아무 정점을 시작점으로 선택(여기서는 V_1 선택)
2. distance 배열에서 최솟값을 갖는 정점 선택
3. 그 정점을 트리에 추가
4. 연결되지 않은 정점들에 대해 nearest, distance 갱신
5. 모든 정점이 포함될 때까지 반복

<br>시간 복잡도는 2중 for 문을 사용하므로 대충 Θ(n^2)이며, 알고리즘에 트리에 포함되지 않은 정점들 중에서 distance[i] 값이 가장 작은 정점을 반복적으로 선택하고 넣는 행위가 있으므로 heap(priority queue)을 쓰면 시간 복잡도를 logn만큼 더 줄일 수 있을 것이다.<br>

> 좋은 자료구조 선택 시 알고리즘의 시간 복잡도를 logn만큼 더 줄일 수 있다.<br>

<br>프림 알고리즘의 궁극적인 버전은 `피보나치 힙`이라는 자료구조를 쓰는 것인데, 관심 있는 사람은 이에 대해 더 찾아보기 바란다.<br>

## 최소 신장 트리를 구하는 방법 - Kruskal's Algorithm

### 크루스칼의 알고리즘 아이디어 요약

가장 짧은 edge부터 선택하면서, Cycle이 생기지 않도록 두 정점을 잇고 정점들의 집합을 병합해나간다.<br><br>

## 크루스칼 알고리즘의 Pseudocode

```plaintext
1. F = ∅     // F는 선택된 edge들의 집합
2. 정점마다 집합 생성 // 여기서 집합은 서로소 집합(Disjoint set)
3. edge를 가중치 기준으로 오름차순 정렬
4. 반복
    다음 edge 선택
    선택된 edge가 서로 다른 집합(Disjoint set)에 속한 두 정점을 연결한다면, 두 집합을 합침(merge)
    이후 해당 edge를 F에 추가
5. 모든 정점이 하나의 집합으로 병합되면, 알고리즘 종료
```

<br>즉, edge를 가중치 기준으로 정렬한 뒤, Cycle이 생기지 않도록 edge를 하나씩 선택, 정점을 연결해서 최소 신장 트리를 구성한다.

여기서 Disjoint set이 좀 생소할 것이다.<br>
Disjoint set(서로소 집합)은 자료구조 중 하나로, 원소들이 서로 겹치지 않는 여러 개의 집합을 관리할 때 쓰는 자료구조이다.<br>
주로 집합을 합치거나 어느 집합에 속해 있는지 확인할 때 사용한다.

Disjoint set은 C++ STL에 없기 때문에 직접 구현하여 사용해야 하며, 따라서 크루스칼의 알고리즘은 간단한 흐름만 소개하고 넘어가도록 하겠다.<br><br>

참고로 크루스칼의 알고리즘의 시간 복잡도는 edge의 개수에 따라 크게 달라지는데, edge가 적을 때는 Θ(nlogn)부터, edge가 많을 때는 Θ(n^2logn)까지 늘어난다.<br><br>

edge가 적으면 크루스칼, edge가 많으면 프림의 알고리즘이 더 좋지만, 사실 프림의 알고리즘에서 피보나치 힙 자료구조를 사용하면 항상 Θ(nlogn)을 유지하므로 프림의 알고리즘이 더 좋긴 하다.

---

### 마무리

`프림의 알고리즘은 정점 중심 확장`<br>
`크루스칼의 알고리즘은 간선 중심 선택`<br><br>

그래프의 특성(정점 수, 간선 수 등)에 따라 더 적합한 알고리즘을 선택할 수 있다.
