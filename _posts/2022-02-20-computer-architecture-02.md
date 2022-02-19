---
title: 컴퓨터구조) Performance
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Computer Science
tags:
    - CS
---
# CPU Time
* 한 컴퓨터 프로그램이 `CPU`를 차지하여 일을 한 시간의 양
* 컴퓨터의 성능을 측정하기 위해 사용된다.
* `CPU Time = Instruction Count x CPI x Clock Cycle Time`
* `CPU Time`이 적을수록 성능이 좋다고 할 수 있다.
* `clock cycle`을 줄이거나 `clock rate`가 올라가면 성능이 향상된다.<br><br><br>

# Instruction Count & CPI
## Instruction Count
* 명령어의 수

## CPI
* Cycles per Instructions
* `Instruction`의 실행주기
* 짧은 `CPI`를 가진 `IC`가 많을수록 성능이 좋다고 할 수 있다.<br><br><br>

# 성능에 영향을 미치는 요소
* 알고리즘 : `IC`, `CPI`
* 프로그래밍 언어 : `IC`, `CPI`
* 컴파일러 : `IC`, `CPI`
* ISA(명령어셋) : `IC`, `CPI`, `T`<br><br><br>

# Clock period
* Duration of a clock cycle
* 하나의 `clock cycle`이 걸리는 시간
* `1/clock rate`로 나타낼 수 있다.<br><br><br>

# Power wall
* `clock rate`는 매년 꾸준히 늘다가 2004년을 기점으로 늘지 않고 있다.
* 왜냐면 전력소모가 많이 줄었기 때문
* 전력소모량은 `Capacitive load`x`Voltage^2`x`Frequency`로 구할 수 있는데 `Voltage`가 많이 줄었기 때문에 `Frequency`가 많이 늘어났음에도 전력소모량은 크게 늘지 않았다.
* 그런데 `Voltage`가 높을수록 전력소모와 열 발생이 많은데 `Voltage`를 더이상 줄일 수가 없게 되었기 때문에 기존에 `clock rate`을 증가시키는 방법으로는 성능 향상을 꾀하기가 어려워졌다.
* 2004년 이후로 `clock rate`을 증가시키지 못한 이유가 `CPU`에서 발생하는 열을 해결하지 못해서였다.
* 그래서 `clock rate`을 올리지 않고 성능을 향상시키기 위한 방법으로 `Multi-core`가 등장하게 된다.<br><br><br>

# Multiprocessors
* `Multicore microprocessors` : 하나의 칩 안에 프로세서가 여러 개 있는 것
* `Parallel programming`이 필요하다. 
    * 하드웨어가 여러 명령을 동시에 실행할 수 있지만 그만큼 프로그래밍이 어렵다.<br><br><br>
    
# CPU 성능 측정
* `SPEC CPU Benchmark`로 측정하거나 `MIPS`(Millions of Instructions per Second : 시간당 몇백만개의 명령을 실행하는가)를 이용해 측정할 수 있는데 `SPEC CPU Benchmark`가 더 정확하다고 할 수 있다.
* `SPEC CPU Benchmark`는 여러 파트에서 측정한 시간값을 바탕으로 총점을 매기는데 `MIPS`는 `IC`와 `Exectution time`을 이용해 계산하기 때문에 컴퓨터마다 `ISA`(명령어셋)가 다르고 또 명령어마다 필요한 `CPI`값이 다를 수 있기 때문에 객관적이라 하기 어렵다.<br><br><br>

# Amdahl's Law
* 컴퓨터의 어느 한 부분의 성능을 향상시키면 전체 성능의 향상은 그 부분이 컴퓨터에서 차지하는 비율만큼 향상된다는 법칙
* `Corollary : make the common case fast`
* 따라서 전체 시스템에서 차지하는 비율이 높은 부분의 성능을 향상시키는 것이 전체 성능 향상에 도움이 된다.<br><br><br>

# 대기 중 전력소모
* `CPU`를 거의 사용하지 않는 상황에서도 생각보다 전력소모가 크다.
    * 예를 들어 `CPU`가 10%만 사용중인데 전력은 전체의 47%를 사용한다는 등
* 그래서 프로세서를 만들 때 전력소모의 비율이 `CPU` 사용량에 비례되게 설계할 필요가 있는데 쉽지 않다.<br><br><br>

# 출처
* [KOCW 영남대학교 최규상 교수님 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1388791&ar=relateCourse)
