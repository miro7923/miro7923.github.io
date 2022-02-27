---
title: OS) CPU Scheduling
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Operating System
tags:
    - CS
    - OS
    - CpuScheduling
---
# 👀 CPU-Burst time
* 프로세스의 실행은 `CPU`를 얻어서 작업을 수행하는 것과 `I/O` 작업을 수행하는 것으로 나눌 수 있다.
* 이 때 `CPU`만 쓰면서 `Instruction`을 실행하는 단계는 `CPU burst`라 하고 `I/O`만 실행하는 단계는 `I/O burst`라 한다.
* 현재 프로세스가 `CPU`를 사용중이라면 다른 프로세스는 사용이 끝날 때까지 기다려야 하겠지만 `I/O` 작업중이라면 다른 프로세스가 `CPU`를 쓸 수 있다.
* 프로세스의 종류는 여러 가지가 있기 때문에 시스템 자원을 효율적으로 쓸 수 있도록 `CPU 스케줄링`이 필요하다.<br><br><br>

# 프로세스의 특성 분류
## I/O-bound process
* `CPU`를 잡고 계산하는 시간보다 `I/O`에 많은 시간이 필요한 job (CPU burst가 아주 짧다)
* 주로 사람과 Interactive하는 job

## CPU-bound process
* 계산 위주의 job (CPU burst가 아주 길다)<br><br><br>

# CPU Scheduler
* `Ready` 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

## Dispatcher
* CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘긴다.
* 이 과정을 context switch(문맥 교환)라 한다.

## CPU 스케줄링이 필요한 경우
* Running -> Blocked (예: I/O 요청하는 시스템 콜)
* Running -> Ready (예: 할당시간 만료로 timer interrupt)
* Blocked -> Ready (얘: I/O 완료 후 인터럽트)
* Terminate<br><br>

* 스케줄링에는 강제로 빼앗지 않고 자진 반납하는 `non-preemptive`와 강제로 빼앗는 `preemptive`가 있다.
* 현대 대부분의 프로세서는 `preemptive`를 사용한다.<br><br><br>

# 스케줄링 성능 척도
* CPU utilization(이용률) : `CPU`가 얼마나 쉼 없이 일하는지
* Throughput(처리량) : 단위 시간동안 처리하는 작업의 양이 얼마나 많은지
* Turnaround time(소요시간, 반환시간) : 프로세스가 CPU를 얻어서 작업을 시작하고 끝날 때까지 걸린 총 시간(대기시간 포함)
* Waiting time(대기 시간) : Ready queue에서 CPU를 얻을 때까지 기다린 시간. 작업 시간은 제외하고 순수하게 기다린 시간만 본다.
* Response tiem(응답 시간) : (time-sharing 시스템에서) 처음으로 CPU를 얻어서 작업을 시작하기까지 걸린 시간<br><br><br>

# 스케줄링 알고리즘
## FCFS (First-Come First-Serve)
* 선착순 방식으로 먼저 온 순서대로 CPU를 얻어서 작업을 시작하고 나중에 온 프로세스들은 이전 작업이 끝날 때까지 기다린다.
* 오래 걸리는 작업이 먼저 와서 계속 수행되고 시간이 짧은 작업들이 뒤에서 기다리고 있는 경우에는 효율적이지 않다. 이런 경우엔 평균 대기 시간이 길어짐
* 짧은 작업이 먼저 와서 처리된 후 오래 걸리는 작업을 처리하게 되면 평균 대기 시간이 짧아지지만 늘 이렇게 프로세스가 도착하지는 않을 것이니까 일반적으로는 효율적이지 않은 방식이라고 할 수 있다. 
* `Convoy effect` : 수행 시간이 긴 작업이 먼저 와서 처리되는 동안 짧은 작업들은 수행되지 못하고 기다리고 있는 현상

## SJF (Shortest-Job-First)
* 도착하는 프로세스마다 CPU burst time을 예측해서 CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄한다. 그래서 평균 대기시간이 가장 짧다.
* 두 가지 방식이 있다.
    * `Non-preemptive` : 일단 CPU를 잡으면 다음 작업이 지금 수행 중인 작업보다 실행 시간이 더 짧아도 지금 작업이 완료되기 전 까지는 CPU를 넘겨주지 않는다.
    * `Preemptive` : 현재 수행 중인 프로세스 다음에 온 프로세스의 수행 시간이 더 짧으면 CPU를 뺏어서 시간이 더 짧은 프로세스에게 넘긴다. 근데 이러다보면 수행 시간이 긴 프로세스는 평생 CPU를 얻지 못 할 수도 있어서(`starvation` 기아 현상) 아주 좋은 방법은 아니다.
    
### 다음 CPU Burst time 예측 방법
* 완벽하게 계산하기 보다는 과거 데이터로부터 추정(estimate)하는 방식을 사용한다.
* n번째 프로세스의 실제 CPU 사용 시간과 n번째 프로세스의 CPU 사용 시간을 예측했던 값들을 일정 비율씩 곱해서 더해주는 방식으로 n + 1번째 프로세스의 CPU burst time을 예측한다.

## Priority Scheduling
* 우선순위 스케줄링이라 하며 프로세스에게 부여된 우선순위가 높은 순서대로 CPU를 할당한다.
* 이것 또한 나중에 도착했지만 우선순위가 더 높은 프로세스에 대해 `Preemptive`와 `Non-preemptive` 방식으로 나뉜다.
* 우선순위 스케줄링의 문제점 또한 우선순위가 낮은 프로세스는 평생 CPU를 얻지 못하는 `Starvation` 현상이 생길 수 있다는 것이다. 
* 그래서 `Aging` 기법을 통해 우선순위가 낮아도 오래 기다렸으면 우선순위를 높여주는 방식을 쓴다.

## Round Robin (RR)
* 대부분의 운영체제에서 사용하는 방식으로 각 프로세스는 동일한 크기의 `CPU 할당 시간(time quantum)`을 가지고 할당된 시간이 끝나면 `CPU`를 다음 프로세스에게 넘겨주고 `Ready queue`의 맨 뒤에 가서 다시 줄을 서는 방식이다.
* 그래서 모든 프로세스는 `Ready queue`의 크기만큼 기다리게 되는데 수행 시간이 짧은 프로세스는 그만큼 빨리 `CPU`를 쓰고 `Ready queue`에서 빠져나가기 때문에 각 프로세스의 대기 시간은 본인의 `CPU` 사용 시간에 비례하게 된다.
* 할당 시간이 너무 크면 `FCFS`와 다를 바가 없고 너무 작으면 `context switch`가 자주 일어나 오버헤드가 커지기 때문에 적절한 중간값을 찾는 것이 좋다.

## Multilevel Queue
* `Ready queue`를 작업의 종류에 따라 여러 개로 분할해서 각 큐마다 다른 스케줄링 알고리즘을 적용한다.
* 예를 들어 `interactive`한 작업들이 담긴 큐라면 `RR` 스케줄링을 적용하고 `CPU burst` 작업들이 담긴 큐라면 `FCFS` 스케줄링을 적용하는 것이다.
* 이 때 `Starvation`을 방지하기 위해서 각 큐에 `CPU time`을 적절한 비율로 할당한다.
    * 예) `RR` 스케줄링 큐에는 80%를 할당하고 `FCFS` 스케줄링 큐에는 20% 할당
    
## Multilevel Feedback Queue
* `Multilevel Queue`에서는 한 번 큐가 결정되면 다른 큐로 이동할 수 없어서 나중에 프로세스의 `burst time`이 바뀌게 되어도 계속 맞지 않는 큐에 있어야 할 수 있다.
* 이것을 보완한 것이 `Multilevel Feedback Queue`인데 프로세스가 다른 큐로 이동이 가능하다.
* `Aging`을 이런 방식으로 구현할 수 있다.
* `Multilevel Feedback Queue scheduler`를 정의하는 파라미터들
    * `Queue`에 있는 작업의 수 : 작업의 수가 적은 큐에 먼저 할당
    * 각 큐의 스케줄링 알고리즘
    * 프로세스를 상위 큐로 보내는 기준 (예: `CPU` 사용시간이 짧을 수록)
    * 프로세스를 하위 큐로 보내는 기준 (예: `CPU` 사용시간이 길수록)
    * 프로세스가 `CPU` 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

## Multiple-Processor Schduling
* `CPU`가 여러 개인 경우 스케줄링이 더욱 복잡해진다.
* `Homogeneous processor`(동종)인 경우
    * 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
    * 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 더 복잡해짐
* `Load sharing`
    * 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
    * 별개의 큐를 두는 방법 vs. 공동 큐를 사용하는 방법
* `Symmetric Multiprocessing (SMP)`
    * 각 프로세서가 각자 알아서 스케줄링 결정
* `Asymmetric multiprocessing`
    * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름
    
## Real-Time Scheduling
* 데드 라인이 있는 작업들에 적용되는 스케줄링
* `Hard real-time systems` : 반드시 정해진 시간 안에 끝내도록 스케줄링
* `Soft real-time computing` : 데드 라인을 조금 어겨도 괜찮기 때문에 `Soft real-time task`는 일반 프로세스에 비해 높은 우선순위를 갖게 한다.

## Thread Scheduling
* `Local Scheduling` : `User level thread`일 경우 사용자 수준의 스레드 라이브러리에 의해 어떤 스레드를 스케줄 할 지 결정
* `Global Scheduling` : `Kernal level thread`인 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄 할 지 결정

## 스케줄링 알고리즘 평가
* `Queueing models`
    * `확률 분포`로 주어지는 `arrival rate`와 `service rate` 등을 통해 각종 `performance index` 값을 계산하는데 요즘은 잘 안 쓴다.
* `Implementation (구현) & Measurement (성능 측정)`
    * `실제 시스템`에 알고리즘을 `구현`하여 실제 작업(`workload`)에 대해서 성능을 `측정` 비교하는 방식으로 많이 쓰는 방식
* `Simulation (모의 실험)`
    * 알고리즘을 `모의 프로그램`으로 작성 후 `trace`를 입력으로 하여 결고 비교<br><br><br>

# 출처
* [운영체제 - 이화여자대학교 KOCW 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
