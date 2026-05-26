---
title: "[OS] CPU 스케줄링 (CPU Scheduling)"
date: 2026-05-26 09:00:00 +0900
categories: [CS, Operating System]
tags: [os, cpu-scheduling, fcfs, sjf, hrn, round-robin, srtf, multilevel-queue]
description: CPU 스케줄링의 개념, 선점/비선점 방식, 주요 알고리즘(FCFS·SJF·HRN·Round Robin·SRTF·Multilevel Queue 등)과 추가 심화 내용을 정리한 포스트입니다.
---

> OS가 CPU를 효율적으로 사용하기 위해 여러 프로세스 중 **어떤 프로세스에 CPU를 얼마나 할당할지** 결정하는 메커니즘이다.

---

## 1. 스케줄링이란?

CPU를 잘 사용하기 위해 **프로세스를 적절히 배정**하는 것.  
프로세스들에게 자원을 최대한 공평하게 배분하여 처리율과 CPU 이용률을 높이고, 오버헤드·응답시간·대기시간을 줄이는 것이 목적이다.

### 설계 조건

- 오버헤드 **최소화**
- CPU 사용률 **최대화**
- 기아(Starvation) 현상 **최소화**

### 시스템별 목표

| 시스템 | 목표 |
|--------|------|
| Batch System | 처리량(Throughput) 극대화 |
| Interactive System | 빠른 응답 시간, 짧은 대기 시간 |
| Real-time System | 데드라인(Deadline) 준수 |

---

## 2. 선점 vs 비선점 스케줄링

| 구분 | 선점 (Preemptive) | 비선점 (Non-preemptive) |
|------|-------------------|------------------------|
| 개념 | OS가 CPU를 강제로 회수 가능 | 프로세스가 자발적으로 CPU 반납 |
| 전환 조건 | 인터럽트, I/O, 이벤트 등 언제든 가능 | 프로세스 종료 또는 I/O 이벤트 발생 시 |
| 처리 시간 예측 | 어려움 | 용이함 |
| 적합 환경 | 인터랙티브, 실시간 시스템 | 배치 시스템 |
| 오버헤드 | 잦은 Context Switching으로 높음 | 상대적으로 낮음 |

> 💡 **비교 요약**
> - 비선점형: 모든 프로세스에 공정하지만 짧은 작업이 긴 작업 종료까지 대기하는 **Convoy 현상** 발생 가능
> - 선점형: 응답이 빠르지만 높은 우선순위 프로세스가 계속 들어오면 **오버헤드** 초래

---

## 3. 프로세스 상태 전이

```
New → Ready → Running → Terminated
               ↑    ↓
             Wait (I/O or Event)
```

| 전이 | 설명 |
|------|------|
| **Admitted** | 프로세스 생성이 승인됨 (New → Ready) |
| **Scheduler Dispatch** | Ready 큐에서 프로세스 하나를 선택해 실행 (Ready → Running) |
| **Interrupt** | 예외/입출력/이벤트 발생 → 현재 프로세스를 Ready 상태로 전환 (Running → Ready) |
| **I/O or Event Wait** | 실행 중 프로세스가 I/O 처리 필요 → Wait 상태로 전환 (Running → Wait) |
| **I/O or Event Completion** | I/O 완료 후 Ready 상태로 전환 (Wait → Ready) |

- **선점 스케줄링** 발생 시점: `Interrupt`, `I/O or Event Completion`, `I/O or Event Wait`, `Exit`
- **비선점 스케줄링** 발생 시점: `I/O or Event Wait`, `Exit`

---

## 4. 스케줄링 결정 시점

CPU 스케줄러는 아래 4가지 상황에서 스케줄링을 결정한다.

| 번호 | 상황 | 유형 |
|------|------|------|
| ① | 실행(Running) → 대기(Waiting) 상태 전환 시 | 비선점 |
| ② | 실행(Running) → 준비(Ready) 상태 전환 시 | 선점 |
| ③ | 대기(Waiting) → 준비(Ready) 상태 전환 시 | 선점 |
| ④ | 프로세스 종료(Terminated) 시 | 비선점 |

> **①과 ④** 상황에서만 스케줄링이 발생하면 **비선점형**, 나머지 모든 상황까지 포함하면 **선점형**이다.

---

## 5. CPU 스케줄링 알고리즘 종류

### 🔵 비선점 스케줄링

#### FCFS (First Come First Served)

- **도착 순서**대로 CPU를 할당하는 선착순 방식
- 구현이 가장 단순하며, 배치 작업처럼 특별한 추가 정보를 얻기 어려운 환경에서 주로 활용
- 시분할 시스템에서 타임슬라이스를 적용하지 않는 백그라운드 프로세스에도 사용 가능
- **단점**: 실행 시간이 긴 프로세스가 앞에 오면 뒤의 짧은 프로세스들이 오래 대기
- **Convoy Effect(호위 효과)** 발생 — 오래 걸리는 프로세스 몇 개로 인해 전체 OS가 느려지는 현상

#### SJF (Shortest Job First)

- **CPU Burst Time이 가장 짧은** 프로세스를 먼저 실행
- **평균 대기 시간에 있어서 이론상 최적의 알고리즘**
- Burst Time이 동일할 경우 FCFS 방식(도착 순서)으로 처리
- **단점 1**: 실제로는 프로세스의 CPU Burst Time을 미리 예측하기 어려움
  - 해결책: 이전 Burst Time을 분석해 다음 Burst Time을 예측 — **지수 평균(Exponential Average)** 방법 활용
- **단점 2**: 긴 작업의 프로세스가 우선순위에 계속 밀려 **기아(Starvation)** 현상 발생 가능

```
지수 평균 공식:
τ(n+1) = α * t(n) + (1 - α) * τ(n)

- t(n)  : 가장 최근의 실제 CPU Burst 시간
- τ(n)  : 과거 누적 예측값
- α     : 가중치 (0 ≤ α ≤ 1)
```

#### HRN (Highest Response-ratio Next)

- SJF의 **기아 현상 보완**을 위해 우선순위를 동적으로 계산
- 대기 시간이 길수록 우선순위가 자동으로 높아져 기아 현상 방지

```
우선순위 = (대기시간 + 실행시간) / 실행시간
```

#### Priority Scheduling — 비선점형

- 각 프로세스에 **우선순위 번호**를 부여하여 높은 순서대로 처리
- 단점: 우선순위가 낮은 프로세스가 무한정 대기하는 **Starvation(기아)** 발생 가능
- 해결책: **Aging** — 오래 대기한 프로세스의 우선순위를 점진적으로 높임

---

### 🔴 선점 스케줄링

#### Round Robin (라운드 로빈)

- 현대 컴퓨터가 가장 많이 사용하는 방식으로, 준비 큐를 **원형 큐**로 간주하여 순환 처리
- 각 프로세스에 **동일한 Time Quantum(타임퀀텀)** 만큼 CPU를 할당하고, 초과하면 강제 선점
- N개의 프로세스가 있고 타임퀀텀이 Q이면, 각 프로세스는 최대 `(N-1) × Q` 시간 내에 다음 슬라이스를 받게 됨
- 새로운 프로세스가 추가될 때는 큐의 맨 뒤에 배치
- **모든 프로세스가 공정하게 기회를 얻어 기아(Starvation) 상태가 발생하지 않는다**는 장점

| Time Quantum 크기 | 결과 |
|-------------------|------|
| 너무 크면 | FCFS와 동일해짐 |
| 너무 작으면 | Context Switching 빈번 → 오버헤드 증가 |
| 최적 기준 | **80% 정도의 CPU Burst Time이 타임퀀텀보다 짧도록** 설정 |

> 💡 타임퀀텀이 커지면 많은 작업이 한 번에 처리되어 평균 반환 시간이 개선될 수 있지만, 짧은 작업이 긴 작업보다 순서상 늦게 처리되는 경우도 생기므로 무조건적이지는 않음.

#### SRTF (Shortest Remaining Time First)

- **SJF의 선점형 버전** (SRF라고도 부름)
- 현재 실행 중인 프로세스의 **남은 시간**보다 더 짧은 프로세스가 도착하면 현재 프로세스를 중단하고 새 프로세스를 실행
- 평균 대기 시간을 줄일 수 있지만, 다음 프로세스의 CPU Burst Time 예측이 어렵다는 문제가 존재

#### Priority Scheduling — 선점형

- 각 프로세스에 **정적/동적 우선순위**를 부여, 더 높은 우선순위 프로세스가 들어오면 현재 프로세스를 강제 중단
- 단점: 우선순위가 낮은 프로세스의 **Starvation(기아)** 발생 가능
- 해결책: **Aging** — 대기 시간이 길어질수록 우선순위를 점진적으로 높임

#### Multilevel Queue (다단계 큐)

- 우선순위에 따라 **여러 개의 준비 큐**를 독립적으로 운영
- 프로세스의 성격(대화형, 배치, 시스템 작업 등)에 따라 그룹을 나눠 각각 다른 큐에 배치

```
우선순위 높음 ↑
  ┌──────────────────────┐
  │   시스템 작업         │ ← 작은 Time Quantum
  ├──────────────────────┤
  │   대화형 작업         │
  ├──────────────────────┤
  │   대화형 편집 작업    │
  ├──────────────────────┤
  │   일괄 처리(Batch)    │
  ├──────────────────────┤
  │   학생 작업           │ ← 큰 Time Quantum
  └──────────────────────┘
우선순위 낮음 ↓
```

- 각 큐는 독자적으로 Round Robin이나 FCFS 등의 알고리즘을 사용 가능
- **단점 1**: 우선순위가 낮은 큐의 프로세스는 처리가 안 되는 **기아(Starvation)** 현상 발생 가능
- **단점 2**: 큐 간 프로세스 이동이 불가하여 유연성이 낮음 (경직된 구조)

#### Multilevel Feedback Queue (다단계 피드백 큐)

- 다단계 큐에서 **프로세스가 큐 간에 이동 가능**하도록 개선한 방식
- Time Quantum을 **다 소진한 프로세스** → 아래 단계 큐로 이동 (CPU 집약적 프로세스로 판단)
- Time Quantum을 **다 채우지 못한 프로세스** → 원래 큐 유지 (I/O 집약적으로 판단)
- 짧은 작업 및 I/O 위주(Interrupt 잦은) 작업에 우선권 부여
- Turnaround 평균 시간 단축
- **현대 OS에서 가장 범용적으로 사용되는 방식**

---

## 6. 스케줄링 성능 척도

| 척도 | 설명 |
|------|------|
| **Response Time (응답 시간)** | 작업이 처음 실행되기까지 걸린 시간 |
| **Turnaround Time (반환 시간)** | 작업 제출부터 완료까지 걸린 총 시간 (실행 시간 + 대기 시간) |

---

## 7. 핵심 요약 정리

| 알고리즘 | 유형 | 장점 | 단점 |
|----------|------|------|------|
| FCFS | 비선점 | 구현 단순 | Convoy Effect, 긴 대기 |
| SJF | 비선점 | 평균 대기 시간 최소 (이론상 최적) | 기아 현상, Burst Time 예측 어려움 |
| HRN | 비선점 | SJF 기아 현상 보완 | 계산 복잡도 증가 |
| Priority | 비선점/선점 | 중요 작업 우선 처리 | Starvation (Aging으로 해결) |
| Round Robin | 선점 | 공평한 CPU 배분, 기아 없음 | Time Quantum 설정이 핵심 |
| SRTF | 선점 | 평균 대기 시간 단축 | Burst Time 예측 어려움 |
| Multilevel Queue | 선점 | 그룹별 차별화 정책 | 큐 간 이동 불가, 기아 가능 |
| Multilevel Feedback Queue | 선점 | 유연한 동적 조정, 현대 OS 채택 | 설계 복잡 |

---

## 8. 추가로 알아야 할 내용

### Starvation과 Aging

- **Starvation(기아 현상)**: 우선순위가 낮은 프로세스가 계속 실행되지 못하는 현상
- **Aging(에이징)**: 오래 대기한 프로세스의 우선순위를 시간이 지남에 따라 점진적으로 높여 기아를 해결하는 기법

### Context Switching (문맥 교환)

- CPU가 현재 실행 중인 프로세스의 상태를 저장하고 다른 프로세스의 상태를 로드하는 과정
- **PCB(Process Control Block)**에 상태 정보 저장
- Context Switching이 잦을수록 **오버헤드 증가** → 실질적인 CPU 작업 시간 감소
- Round Robin에서 Time Quantum이 너무 작으면 이 문제가 심각해짐

### CPU Burst vs I/O Burst

- **CPU Burst**: 프로세스가 CPU를 집중적으로 사용하는 구간
- **I/O Burst**: 프로세스가 I/O 작업을 기다리는 구간
- 대부분의 프로세스는 CPU Burst와 I/O Burst가 **교대로** 발생
- I/O Burst가 많은 프로세스(입출력 집약적) vs CPU Burst가 많은 프로세스(CPU 집약적)를 구분하여 스케줄링 정책 설계

### Convoy Effect (호위 효과)

- **FCFS에서 발생**하는 문제
- 실행 시간이 긴 프로세스(CPU-bound)가 CPU를 점유하는 동안, 짧은 프로세스들이 줄줄이 대기하는 현상
- 전체 시스템 처리율 저하 유발

### Dispatcher (디스패처)

- 스케줄러가 선택한 프로세스에게 **실제로 CPU 제어권을 넘겨주는** 모듈
- Context Switching 수행, 사용자 모드 전환, 프로그램 재시작 등의 역할
- **Dispatch Latency**: 디스패처가 프로세스를 전환하는 데 소요되는 시간 → 최소화가 목표

### 스케줄링 관련 시간 개념 정리

| 용어 | 설명 |
|------|------|
| **Arrival Time** | 프로세스가 Ready 큐에 도착한 시간 |
| **Burst Time** | 프로세스가 CPU를 필요로 하는 실행 시간 |
| **Waiting Time** | Ready 큐에서 대기한 총 시간 |
| **Response Time** | 처음 CPU를 할당받기까지 걸린 시간 |
| **Completion Time** | 프로세스가 완료된 시간 |
| **Turnaround Time** | Completion Time - Arrival Time |

### 실제 OS에서의 스케줄링

- **Linux**: CFS(Completely Fair Scheduler) 사용 — 각 프로세스에 공정하게 CPU 시간을 배분, Red-Black Tree 기반
- **Windows**: 멀티레벨 피드백 큐 기반의 우선순위 스케줄링 사용 (32단계 우선순위)
- **macOS/iOS**: FIFO + Round Robin 혼합, GCD(Grand Central Dispatch)로 스레드 관리

### 면접 단골 질문 포인트

- **Round Robin에서 최적의 Time Quantum이란?** → 너무 크지도 작지도 않게, Context Switching 오버헤드와 응답 시간의 균형. 80% 정도의 Burst Time이 Quantum보다 짧도록 설정하는 것이 일반적
- **SJF가 이론상 최적이지만 실제 사용이 어려운 이유?** → 미래의 CPU Burst 시간을 정확히 알 수 없음. 지수 평균(Exponential Average)으로 예측하는 방법이 존재하지만 완벽하지 않음
- **Multilevel Feedback Queue가 현대 OS에서 가장 많이 쓰이는 이유?** → 유연성과 실용성의 균형. 프로세스 특성에 따른 자동 분류가 가능하며, 짧은 작업과 I/O 집약적 작업을 우선 처리하면서 CPU 집약적 작업도 결국 처리됨

---

## 참고 출처

- [gyoogle.dev - CPU Scheduling](https://gyoogle.dev/blog/computer-science/operating-system/CPU%20Scheduling.html)
- [devdo.log - CPU 스케쥴링 정리](https://velog.io/@mooh2jj/CPU-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%A7%81-%EC%A0%95%EB%A6%AC)
- [교니 - CPU 스케줄링 알고리즘 요약정리](https://velog.io/@qq7455/CPU-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EC%9A%94%EC%95%BD%EC%A0%95%EB%A6%AC)
