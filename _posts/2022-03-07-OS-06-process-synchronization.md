---
title: OS) Process Synchronization
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Operating System
tags:
    - CS
    - OS
    - Process
    - Synchronization
---
# 👀 Process Synchronization 문제
* 컴퓨터에 저장되어 있는 어떤 데이터를 변경하려면 그 데이터에 접근해서 변경하는 연산을 한 뒤 연산 결과를 다시 그 데이터가 있는 자리에 갱신시켜줘야 한다.
* 그런데 이 때 하나의 프로세스만 접근해서 작업을 하면 문제가 없지만 컴퓨터에는 수많은 프로세스가 있고 하나의 데이터에 여러 프로세스가 접근하는 상황이 생길 수 있다.
* 이 때 아무런 제어 없이 여러 프로세스가 접근해서 하나의 데이터를 변경시키면 서로 다른 시점에 데이터를 변경하게 될 수 있고 그러다보면 데이터가 사용자의 의도와 다르게 변경될 수 있다(데이터 불일치 문제). 그래서 이걸 막고 데이터의 일관성 유지를 위해서 공유 데이터에 접근할 때 협력 프로세스 간의 실행 순서를 정해주어야 한다.

## 🔸 Race condition
* 여러 프로세스들이 동시에 공유 데이터에 접근하는 상황
* 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
* `Race condition`을 막기 위해 `concurrent process(동시 접근)`는 `동기화(synchronize)`되어야 한다.<br><br><br>

# Race Condition이 발생하는 상황
## 1. 커널 수행 중 인터럽트 발생 시
* 커널 데이터를 처리하는 도중에 `인터럽트`가 생긴 경우
* 해결책 : 먼저 작업 중이던 커널 코드의 작업이 끝나기 전 까지는 `인터럽트`를 받지 않는다.

## 2. 프로세스가 시스템 콜을 하여 커널 모드로 수행 중인데 context switch가 일어나는 경우
* A 프로세스가 `PC`를 증가시키는 도중에 다른 커널 코드 B에게 `CPU`를 뺏겼다가 뺏어간 쪽의 작업이 끝난 후 다시 돌려 받으면 A는 B가 증가시키기 전의 `PC` 정보를 가지고 있기 때문에 A가 증가시키는 `PC`는 정확한 값으로 증가되지 않는다.
* 해결책 : 커널 모드에서 수행 중일 때는 `CPU`를 `preempt`하지 않고 커널 모드에서 사용자 모드로 돌아갈 때 `preempt`한다.

## 3. Multiprocessor에서 shared memory 내의 커널 데이터
* 위 경우들은 같은 `CPU` 내에서만 할 수 있는 동작들이기 때문에 멀티 프로세서 환경에서는 적용이 어렵다. 
* 여러 프로세스들이 하나의 공유 메모리에 접근해야 할 때 각각의 `CPU`에서 동시에 접근해서 데이터를 변경할 수 있다.
* 해결책 1 : 한 번에 하나의 `CPU`만이 커널에 들어갈 수 있게 하는 방법
* 해결책 2 : 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 `lock/unlock`을 하는 방법<br><br><br>

# The Critical-Section Problem
* n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
* 각 프로세스의 `code segment`에는 공유 데이터를 접근하는 코드인 `critical section`이 존재
* 하나의 프로세스가 `critical section`에 있을 때 다른 모든 프로세스는 `critical section`에 들어갈 수 없어야 한다.

## 프로그램적 해결법의 충족 조건
* `Mutual Exclusion(상호 배제/배타적 접근)`
    * 프로세스 Pirk `critical section` 부분을 수행 중이면 다른 모든 프로세스들은 그들의 `critical section`에 들어가면 안 된다.
* `Progress(진행)`
    * 아무도 `critical section`에 있지 않은 상태에서 `critical section`에 들어가고자 하는 프로세스가 있으면 `critical section`에 들어가게 해 주어야 한다.
* `Bounded Waiting(유한 대기)`
    * 프로세스가 `critical section`에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 `critical section`에 들어가는 횟수에 한계가 있어야 한다.
    * 무한대로 기다리지 않게 해서 `starvation`을 방지<br>

* 가정
    * 모든 프로세스의 수행 속도는 0보다 크다.
    * 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

## 기본적인 해결 방법
* 두 개의 프로세스 `P0`, `P1`이 있다고 가정했을 때 

```c
do {
    entry section   /* lock */
    critical section
    exti section    /* unlock */
    remainder section
} while(1);
```

* 프로세스들은 수행의 동기화를 위해 몇몇 변수를 공유할 수 있다. - `Synchronization variable`

### Algorithm 1

```c
int turn;   /* 누구 차례인지 표시할 변수.  Synchronization variable */
initially turn = 0;

do {
    while (turn != 0);  /* 내 턴이 아닌 동안 대기 */
    critical section
    turn = 1;   /* 상대편으로 턴 넘김 */
    remainder section
} while (1);
```

* 위 코드대로만 진행되면 문제가 없어 보이지만 반드시 상대 프로세스가 들어와서 `turn`을 바꿔 주어야만 다른 프로세스가 `critical section`에 들어갈 수 있기 때문에 만약 A 프로세스가 더 빈번하게 `critical section`에 들어가야 하는 경우 B 프로세스는 상대적으로 덜 들어가기 때문에 `turn`이 바뀌지 않아 A 프로세스가 들어갈 수 없는 상황이 생긴다.

### Algorithm 2

```c
boolean flag[2];    /* critical section에 들어가고자 하는 의중을 표시하는 flag */
initially flag[모두] = false; /* 처음엔 critical section에 아무도 없음 */

do {
    flag[i] = true;     /* 진입 표시 */
    while (flag[j]);    /* 상대 프로세스도 진입해 있으면 대기 */
    critical section
    flag[i] = false;    /* 나왔다고 표시 */
    remainder section
} while (1);
```

* 위 코드에서는 만약 A 프로세스가 첫번째 줄만 실행하고 `CPU`를 뺏긴 경우 상대 프로세스도 첫번째 줄을 실행하고 두번째 줄에서 대기하게 될 것이다. 즉 둘 다 무한히 대기하게 된다.

### Algorithm 3 (Peterson's Algorithm)

```c
do {
    flag[i] = true;     /* 진입 표시 */
    turn = j;           /* 상대편으로 턴 변경 */
    while (flag[j] && turn == j);   /* 들어가고자 하는 프로세스와 턴 모두 확인 */
    critical section
    flag[i] = false;
    remainder section
} while (1);
```

* 알고리즘 1과 2를 합친 방법으로 앞선 두 가지 방법에서 있었던 문제들을 모두 해결할 수 있다.
* `Busy Waiting(= spin lock)`
    * 하지만 `critical section`에 프로세스가 이미 있어서 못 들어가는 것이 확실한 상황에서도 계속 `CPU`를 얻어서 `while`문 조건을 확인해야 하기 때문에 지속적으로 `CPU`와 `memory`를 소모하게 된다.

### 하드웨어적으로 해결하기
* `Instruction` 하나로 `Synchronization variable` 읽기와 쓰기를 동시에 실행하면 위의 알고리즘으로 해결하고자 했던 문제들을 간단히 해결할 수 있다. 

```c
boolean lock = false;

do {
    while (Test_and_set(lock); /* lock이 걸려있다면 lock을 걸고 기다릴 것이고 걸려있지 않다면 lock을 걸고 들어갈 것임 */
    critical section
    lock = false;
    remainder section
} while (1);
```
<br><br>

## Semaphores
* 프로그래머가 매번 앞의 작업들을 하려면 귀찮으니까 그 작업들을 추상화시킨 것
* `integer variable` 형태로 자원의 갯수를 가지고 있다.
* 아래의 두 가지 `atomic` 연산에 의해서만 접근할 수 있다.

```c
/* P(S) : 공유 데이터를 획득하는 연산 */
while (S <= 0) do wait; /* 사용할 수 있는 자원이 없으면 대기 */
S--;

/* V(S) : 공유 데이터를 사용 후 반납 */
S++;
```

### 세마포어를 이용한 코드 - Busy-wait

```c
semaphore mutex;    /* initially 1 : 1개가 critical section에 들어갈 수 있다 */

do {
    P(mutex);   /* 자원이 남아 있으면 하나 감소시키고 입장 */
    critical section
    V(mutex);   /* 자원 반납 및 증가 */
    remainder section
} while (1);
```

* 대기하는 프로세스가 있으면 자원이 생겼는지 계속 확인해야 하기 때문에 `busy-wait`하게 된다.

### 세마포어를 이용한 코드 - Block & wekeup
* 세마포어를 다음과 같이 정의한다.

```c
typedef struct
{
    int value;          /* semaphore */
    struct process *L;  /* 프로세스 대기열 */
} semaphore;
```

* `block`과 `wakeup`을 다음과 같이 가정한다.
    * `block` : 커널은 `block`을 호출한 프로세스를 `suspend` 시킨 후 이 프로세스의 `PCB`를 세마포어에 대한 대기열에 넣음
    * `wakeup` : `block`된 프로세스를 `wakeup` 시킨 후 이 프로세스의 `PCB`를 `ready queue`로 옮김

* 세마포어 연산 정의

```c
/* P(S) */
S.value--;  /* 입장 전에 자원의 수를 감소시킴 */
if (S.value < 0)    /* 현재 쓸 수 있는 자원이 없으면 대기시킨다 */
{
    add this process to S.L;
    block();
}

/* V(S) */
S.value++;
if (S.value <= 0)   /* P 연산에서 자원을 미리 감소시키고 들어가기 때문에 자원을 반납했는데도 갯수가 0 이하라면 기다리고 있는 프로세스가 있는 것 */
{
    remove a process P from S.L;
    wakeup(P);
}
```

### 👀 Busy-wait vs. Block/wakeup
* `critical section`의 길이가 긴 경우 `Block/wakeup`이 적당
* `critical section`의 길이가 매우 짧은 경우 `Block/wakeup` 오버헤드가 `Busy-wait` 오버헤드보다 더 커질 수 있다.
* 일반적으로는 `Block/wakeup` 방식이 좋다.<br><br>

## 세마포어의 두 종류
* `Counting semaphore`
    * 도메인이 0 이상인 임의의 정수값
    * 주로 리소스의 갯수를 세는 데 사용한다.
    
* `Binary semaphore`
    * 0 또는 1 값만 가질 수 있는 세마포어
    * 주로 `mutual exclusion (lock/unlock)`에 사용

## Deadlock and Starvation
### Deadlock
* 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
* 1과 2 두 개의 자원을 얻어야 하는 A 프로세스가 있을 때 만약 A 프로세스가 1번 자원만 얻고 B 프로세스에게 `CPU`를 뺏긴 후 B 프로세스가 2번 자원을 얻으면 A 프로세스는 `CPU`를 다시 돌려받아도 2번 자원을 얻지 못해서 다음으로 진행할 수 없다. B 프로세스 또한 1번 자원이 필요하다면 영원히 기다리게 된다.
* 이러한 현상을 해결하기 위해서 자원을 획득하는 순서를 정해줘서 1번 자원을 먼저 얻어야만 2번 자원을 얻을 수 있게 하는 방식을 사용할 수 있다.

### Starvation
* `indefinite blocking`
* 프로세스가 `suspend`된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상<br><br><br>

# Classical Problems of Synchronization
## 🔸 Bounded buffer problem
* `Producer-Consumer Problem`이라고도 하며 데이터 입력을 위한 버퍼를 생성하는 생산자와 그 버퍼를 읽어서 수정된 데이터를 반영할 소비자로 나누는 개념이다. 
* 생산자는 빈 버퍼가 있는지 확인 후(없으면 기다림) 공유데이터에 lock을 걸고 버퍼에 데이터를 입력한 다음 lock을 풀고 든 버퍼를 하나 증가시킨다.
* 소비자는 든 버퍼가 있는지 확인 후(없으면 기다림) 공유데이터에 lock을 걸고 버퍼에서 데이터를 꺼낸 뒤 lock을 풀고 빈 버퍼를 하나 증가시킨다.

### 공유 데이터
* 버퍼 및 버퍼 조작 변수(empty/full buffer의 시작 위치)

### 동기화 변수
* mutual exclusion : 공유데이터의 mutual exclusion을 위해 필요
* resource count : 남은 empty/full buffer의 수를 표시하기 위해 필요

```c
/* Producer */
do {
    produce an item in x
    ...
    P(empty);
    P(mutex);
    ...
    add x to buffer
    ...
    V(mutex);
    V(full);
} while (1);

/* Consumer */
do {
    P(full);
    P(mutex);
    ...
    remove an item from buffer to y
    ...
    V(mutex);
    V(empty);
    ...
    consume the item in y
    ...
} while (1);
```

## 🔸 Readers-Writers Problem
* 한 프로세스가 `DB`에 `write` 중일 때 다른 프로세스가 접근하면 안 됨
* `read`는 동시에 여럿이 해도 됨
* solution
    * `Writer`가 `DB`에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기 중인 `Reader`들을 다 `DB`에 접근하게 해준다.
    * `Writer`는 대기 중인 `Reader`가 하나도 없을 때 `DB` 접근이 허용된다.
    * 일단 `Writer`가 `DB`에 접근 중이면 `Reader`들은 접근이 금지된다.
    * `Writer`가 `DB`에서 빠져 나가야만 `Reader`의 접근이 허용된다.

### 공유 데이터
* `DB` 자체
* `readcount` : 현재 `DB`에 접근 중인 `Reader`의 수

### 동기화 변수
* `mutex` : 공유 변수 `readcount`를 접근하는 코드(critical section)의 `mutual exclusion` 보장을 위해 사용
* `db` : `Reader`와 `Writer`가 공유 `DB` 자체를 올바르게 접근하는 역할

```c
int readcount = 0;
DB 자체;
semaphore mutex = 1, db = 1;

/* Writer */
P(db);
...
writing DB ...
...
V(db);

/* Reader */
P(mutex);
readcount++;
if (readcount == 1) P(db);  /* Writer 대기시킴 */
V(mutex);
...
reading DB ...
...
P(mutex);
readcount--;
if (readcount == 0) V(db);  /* Writer 입장 가능 */
V(mutex);
```

* 하지만 위 코드대로만 하면 `Reader`가 계속 들어와서 `Writer`가 영원히 기다리게 될 수 있기 때문에(starvation) `Writer`의 우선순위를 높여줘서 너무 오래 기다리지 않게 한다.

## 🔸 Dining-Philosophers Problem
* 다섯 명의 철학자가 원형 테이블에 앉아서 생각과 식사를 반복하는데 젓가락이 5개(2개 세트 아님)밖에 없음
* 철학자는 내 양 옆에 있는 젓가락을 옆에 있는 철학자들이 쓰고 있지 않아야 젓가락을 사용해 밥을 먹을 수 있다.
* 이 때 어떤 철학자의 양 옆에 있는 철학자들이 계속 밥을 먹어서 어떤 철학자가 젓가락을 영원히 쓰지 못하면 굶어 죽는다는.. 그런 문제이다.
* 해결 방안
    * 4명의 철학자만 테이블에 동시에 앉을 수 있도록 한다.
    * 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다.
    * 비대칭 : 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록 한다.<br><br><br>
    
# Monitor
## 세마포어의 문제점
* 코딩하기 힘들다. - 문제가 생겼을 때 버그 잡기 힘듦
* 정확성(correctness)의 입증이 어렵다.
* 자발적 협력(voluntary cooperation)이 필요하다.
* 한 번의 실수가 모든 시스템이 치명적인 영향을 끼친다.

### 예

```
V(mutex)
critical section
P(mutex)
```
* `Mutual exclusion`이 깨짐<br>

```
P(mutex)
critical section
P(mutex)
```
* `Deadlock`<br>

* 위 경우들처럼 순서가 바뀌거나 같은 연산을 두 번 쓰는 실수가 생기면 오작동이 생긴다.

## Monitor
* 동시 수행 중인 프로세스 사이에서 `abstract data type`의 안전한 공유를 보장하기 위한 `high-level synchronization construct`
* 객체 지향 프로그래밍에서 `class`에 멤버 변수와 함수를 담아서 사용하듯이 공유 데이터를 모니터 안에 선언하고 공유 데이터에 접근하려면 모니터 내부의 함수를 통해서만 하도록 하는 방법
* 모니터 내의 함수는 한 번에 하나만 실행할 수 있기 때문에 `lock`을 걸 필요가 없다. 그래서 프로그래머 입장에서는 좀 더 간편하게 프로그램을 짤 수 있다.<br><br><br>

# 출처
* [운영체제 - 이화여자대학교 KOCW 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
