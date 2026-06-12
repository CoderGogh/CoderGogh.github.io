---
title: "[Algorithm] 코딩 테스트 빈출 알고리즘 핵심 정리"
date: 2026-06-12 12:00:00 +0900
categories: [Algorithm, Coding Test]
tags: [hash, stack, queue, heap, sorting, brute-force, greedy, dp, dfs, bfs, binary-search, graph]
description: 코딩 테스트에서 가장 자주 등장하는 해시, 스택/큐, 힙, 정렬, 완전 탐색, 그리디, DP, DFS/BFS, 이분 탐색, 그래프의 개념과 풀이 접근법을 정리한 포스트입니다.
---

> 코딩 테스트는 알고리즘을 많이 아는 것보다, 문제를 보고 어떤 알고리즘을 적용해야 하는지 판단하는 능력이 더 중요하다.

---

## 1. 해시 (Hash)

해시는 데이터를 **Key-Value 형태로 저장하여 매우 빠르게 조회하기 위한 자료구조**이다.

배열은 값을 찾기 위해 순차 탐색이 필요하지만, 해시는 Key를 해시 함수(Hash Function)에 전달하여 특정 인덱스로 변환한 후 바로 접근할 수 있다.

### 언제 사용할까?

다음과 같은 상황에서 해시를 고려할 수 있다.

- 특정 데이터의 존재 여부 확인
- 문자열 빈도수 계산
- 중복 제거
- 데이터 그룹화

대표적으로 프로그래머스의 **완주하지 못한 선수**, **의상**, **베스트앨범** 문제가 해시 유형에 속한다.

### 시간 복잡도

| 연산 | 시간 |
|--------|--------|
| 조회 | O(1) |
| 삽입 | O(1) |
| 삭제 | O(1) |

### 예제

```java
HashMap<String, Integer> map = new HashMap<>();

map.put("Alice", 90);
map.put("Bob", 80);

System.out.println(map.get("Alice"));
```

---

## 2. 스택(Stack)과 큐(Queue)

스택과 큐는 데이터의 저장 순서와 꺼내는 순서가 정해져 있는 대표적인 선형 자료구조이다.

### Stack

스택은 **LIFO(Last In First Out)** 구조이다.

가장 마지막에 들어온 데이터가 가장 먼저 나간다.

대표 사용 사례

- 괄호 검사
- 뒤로 가기 기능
- DFS
- 수식 계산

```java
Stack<Integer> stack = new Stack<>();

stack.push(1);
stack.push(2);

System.out.println(stack.pop());
```

### Queue

큐는 **FIFO(First In First Out)** 구조이다.

가장 먼저 들어온 데이터가 가장 먼저 나간다.

대표 사용 사례

- BFS
- 작업 대기열
- 프로세스 스케줄링

```java
Queue<Integer> queue = new LinkedList<>();

queue.offer(1);
queue.offer(2);

System.out.println(queue.poll());
```

### 시간 복잡도

| 연산 | 시간 |
|--------|--------|
| 삽입 | O(1) |
| 삭제 | O(1) |

---

## 3. 힙 (Heap)

힙은 **최댓값 또는 최솟값을 빠르게 찾기 위한 완전 이진 트리 기반 자료구조**이다.

일반적인 정렬 상태를 유지하지는 않지만, 루트 노드에 항상 최댓값 또는 최솟값이 위치한다.

### 언제 사용할까?

- 우선순위 큐
- 다익스트라 알고리즘
- 작업 스케줄링
- 최소값/최대값 반복 추출

### 시간 복잡도

| 연산 | 시간 |
|--------|--------|
| 조회 | O(1) |
| 삽입 | O(logN) |
| 삭제 | O(logN) |

### 예제

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(5);
pq.offer(1);
pq.offer(3);

System.out.println(pq.poll());
```

---

## 4. 정렬 (Sorting)

정렬은 데이터를 특정 기준에 따라 재배치하는 알고리즘이다.

코딩 테스트에서는 직접 구현보다 내장 정렬 함수를 사용하는 경우가 많으며, 정렬 기준을 설정하는 능력이 중요하다.

### 대표 정렬 알고리즘

| 알고리즘 | 시간 복잡도 |
|----------|------------|
| 버블 정렬 | O(N²) |
| 선택 정렬 | O(N²) |
| 삽입 정렬 | O(N²) |
| 퀵 정렬 | O(NlogN) |
| 병합 정렬 | O(NlogN) |
| 힙 정렬 | O(NlogN) |

### 예제

```java
Integer[] arr = {5, 2, 8, 1, 9};

Arrays.sort(arr, (a, b) -> b - a);

System.out.println(Arrays.toString(arr));
```

---

## 5. 완전 탐색 (Brute Force)

완전 탐색은 가능한 모든 경우를 직접 확인하는 방식이다.

가장 단순하지만 가장 확실한 방법이며, 입력 크기가 작을 때 유효하다.

### 특징

- 구현이 단순함
- 정답을 보장함
- 시간 복잡도가 높음

### 구현 방식

- 반복문
- 재귀
- 순열
- 조합

### 예제

```java
for (int i = 0; i < nums.length; i++) {
    for (int j = i + 1; j < nums.length; j++) {

        if (nums[i] + nums[j] == target) {
            System.out.println(i + " " + j);
        }
    }
}
```

---

## 6. 탐욕법 (Greedy)

탐욕법은 매 순간 가장 좋아 보이는 선택을 하는 알고리즘이다.

중요한 점은 현재의 최선의 선택이 최종적으로도 최적해를 보장해야 한다는 것이다.

### 대표 문제

- 거스름돈
- 회의실 배정
- 체육복
- 큰 수 만들기

### 전형적인 풀이 흐름

```text
정렬
↓
현재 최선 선택
↓
반복
```

### 예제

```java
int money = 1260;
int count = 0;

int[] coins = {500, 100, 50, 10};

for (int coin : coins) {
    count += money / coin;
    money %= coin;
}
```

---

## 7. 동적 계획법 (Dynamic Programming)

DP는 동일한 계산을 반복하지 않기 위해 결과를 저장하는 기법이다.

### 적용 조건

#### 1. 최적 부분 구조

큰 문제를 작은 문제로 나눌 수 있어야 한다.

#### 2. 중복 부분 문제

동일한 하위 문제가 반복적으로 등장해야 한다.

### 풀이 순서

1. DP 배열 정의
2. 초기값 설정
3. 점화식 도출
4. 반복문 수행

### 피보나치 예제

```java
int[] dp = new int[n + 1];

dp[1] = 1;
dp[2] = 1;

for (int i = 3; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
}
```

---

## 8. DFS와 BFS

그래프 탐색의 대표적인 두 가지 방법이다.

### DFS (Depth First Search)

한 경로를 끝까지 탐색한 후 되돌아온다.

주로 재귀 또는 Stack을 사용한다.

```java
static void dfs(int node) {

    visited[node] = true;

    for (int next : graph[node]) {

        if (!visited[next]) {
            dfs(next);
        }
    }
}
```

### BFS (Breadth First Search)

가까운 노드부터 탐색한다.

Queue를 사용하며, 가중치가 없는 최단 거리 문제에 활용된다.

```java
Queue<Integer> queue = new LinkedList<>();

queue.offer(start);

while (!queue.isEmpty()) {

    int cur = queue.poll();

    for (int next : graph[cur]) {

        if (!visited[next]) {
            visited[next] = true;
            queue.offer(next);
        }
    }
}
```

### 비교

| 구분 | DFS | BFS |
|--------|--------|--------|
| 자료구조 | Stack | Queue |
| 탐색 방식 | 깊게 | 넓게 |
| 최단 거리 | 불리 | 유리 |

---

## 9. 이분 탐색 (Binary Search)

이분 탐색은 정렬된 데이터에서 탐색 범위를 절반씩 줄여 나가는 알고리즘이다.

### 핵심 조건

데이터가 반드시 정렬되어 있어야 한다.

### 시간 복잡도

| 방식 | 시간 |
|--------|--------|
| 순차 탐색 | O(N) |
| 이분 탐색 | O(logN) |

### 예제

```java
while (left <= right) {

    int mid = left + (right - left) / 2;

    if (arr[mid] == target) {
        return mid;
    }

    if (arr[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
```

### 심화: 매개변수 탐색

코딩 테스트에서는 단순 탐색보다 다음과 같은 형태로 자주 등장한다.

```text
조건 만족 여부 판단
↓
이분 탐색
↓
최적값 탐색
```

대표 문제

- 입국심사
- 공유기 설치
- 징검다리

---

## 10. 그래프 (Graph)

그래프는 노드(Vertex)와 간선(Edge)을 이용하여 관계를 표현하는 자료구조이다.

네트워크, 경로 탐색, 연결 관계 문제의 대부분이 그래프로 모델링된다.

### 표현 방법

#### 인접 행렬

```java
int[][] graph = new int[n][n];
```

- 연결 확인 O(1)
- 공간 O(N²)

#### 인접 리스트

```java
ArrayList<Integer>[] graph = new ArrayList[n + 1];
```

- 공간 효율 우수
- DFS/BFS에 적합

### 대표 알고리즘

| 알고리즘 | 목적 |
|----------|----------|
| DFS | 그래프 탐색 |
| BFS | 최단 거리 |
| 다익스트라 | 단일 출발 최단 경로 |
| 플로이드 워셜 | 모든 정점 최단 경로 |
| 크루스칼 | 최소 신장 트리 |
| 위상 정렬 | 순서 결정 |

---

## 11. 문제를 보고 어떤 알고리즘을 선택해야 할까?

| 문제 특징 | 추천 알고리즘 |
|-----------|--------------|
| 빠른 조회 | 해시 |
| 역순 처리 | 스택 |
| 순차 처리 | 큐 |
| 우선순위 존재 | 힙 |
| 모든 경우 확인 | 완전 탐색 |
| 현재 최선 선택 | 그리디 |
| 중복 계산 존재 | DP |
| 그래프 탐색 | DFS/BFS |
| 정렬된 데이터 탐색 | 이분 탐색 |
| 연결 관계 | 그래프 |

---

## 12. 마무리

코딩 테스트에서 중요한 것은 알고리즘을 암기하는 것이 아니라 문제의 특징을 보고 적절한 알고리즘을 떠올리는 능력이다.

특히 다음 흐름은 반드시 익숙해질 필요가 있다.

```text
빠른 조회 → 해시
모든 경우 → 완전 탐색
최적화 → DP
그래프 탐색 → DFS/BFS
최단 거리 → BFS / 다익스트라
최적값 탐색 → 이분 탐색
```

알고리즘 자체를 외우기보다 "어떤 문제에서 등장하는가"를 중심으로 학습하면 실전에서 훨씬 빠르게 접근할 수 있다.
