---
title: 컴퓨터 구조) 컴퓨터 구조 Preview
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Computer Science
tags:
    - CS
---
# 컴퓨터의 발전
* `무어의 법칙` 덕분에 숱한 기술의 발전이 가능했다.
* 중요하니까 외우자.<br><br>

## 👀 무어의 법칙
* 싱글 칩의 트랜지스터 수가 2년마다 2배로 증가한다는 법칙(실제로는 2배보다 더 많이 증가)
* 성능의 급격한 발전으로 인해 과거에 비해 더 싼 가격에 더 좋은 성능을 발휘할 수 있게 되었다.<br><br><br>

# 컴퓨터의 종류
## 개인 컴퓨터
* 일반적으로 사용하는 컴퓨터
* 비교적 저렴하고 저성능<br><br>

## 서버 컴퓨터
* 네트워크 통신이 주 목적
* 고가, 대용량, 고성능<br><br>

## 수퍼 컴퓨터
* 군사용, 기상청 날씨 예측용 등 특수 목적으로 사용되는 컴퓨터<br><br>

## 임베디드 컴퓨터
* 큰 시스템에 컴퓨터가 하나의 컴포넌트로 구성된 것
* 적은 전력소모, 저성능, 저가
* ex) 스마트폰, 태블릿 PC<br><br><br>

# 데이터의 단위
* `bit` - `byte` - `Kilobyte` - `Megabyte` - `Gigabyte` - `Terabyte` - `Petabyte` - `Exabyte`<br><br>

* `Byte` : 8`bit`
* `Kilobyte` : 2^10 or 1,024 `bytes`
* `Megabyte` : 2^20 or 1,048,576 `bytes`
* `Gigabyte` : 2^30 or 1,073,741,824 `bytes`
* `Terabyte` : 2^40 or 1,099,511,627,776 `bytes`
* `Petabyte` : 2^50 or 1024 `terabytes`
* `Exabyte` : 2^60 ir 1024 `petabytes`<br><br><br>

# 미래의 컴퓨터
## Personal Mobile Device (PMD)
* 스마트폰, 태블릿 PC, 전자안경 등
* 배터리로 작동되며 인터넷에 연결해서 사용<br><br>

## Cloud computing
* Warehouse Scale Computers (WSC) : 특정한 데이터를 서버에서 처리하고 필요할 때 서버에서 다운받아서 사용
* Software as a Service (SaaS) : 필요한 소프트웨어를 서비스 형태로 제공받는다.
* 복잡한 연산은 `Cloud`에서 처리하고 `PMD`에서는 간단한 `UI` 같은 것만 보여주는 것
* 아마존과 구글의 클라우드 서비스<br><br><br>

# 컴퓨터의 성능
## 알고리즘 측면
* 실행되는 `operation`의 수가 적을수록 성능이 높다.<br><br>

## 프로그래밍 언어, 컴파일러, 구조
* `operation`당 실행되는 `instruction`의 수가 많을수록 성능이 높다.<br><br>

## 프로세서와 메모리
* 단위시간당 `instruction`을 얼마나 빨리 실행하는지<br><br>

## 입출력 시스템
* 단위시간당 `I/O operation`을 얼마나 많이 실행하는지에 따라 결정된다.<br><br><br>

# 컴퓨터 구조
## Application software
* 일반적으로 사용자가 사용하는 프로그램
* 고급언어로 작성된 프로그램<br><br>

## System software
* `Compiler` : 고급언어를 기계어로 번역하는 것
* `Operating System` : 운영체제. 입출력, 메모리 관리, 자원 스케줄링 등을 한다.<br><br>

## Hardware
* `Processor`, `memory`, `I/O controllers`<br><br><br>

# Levels of Program Code
## High-level language
* `C`언어 같이 우리가 알아보기 쉬운 언어로 작성한 코드
* 컴파일러를 거쳐 어셈블리어로 번역된다.<br><br>

## Assembly language
* 고급언어보다는 알아보기 어렵지만 그래도 사람이 알아볼 수는 있는 정도의 단어들로 작성된 코드
* 어셈블러(Assembler)를 거쳐 기계어로 번역된다.<br><br>

## Hardware representation
* 0과 1로 이루어진 코드(Binary digits, bits)
* 비주얼 스튜디오 같은 `IDE`엔 컴파일러와 어셈블러가 함께 들어있어서 일반적으로는 함께 실행되지만 단계를 나눠서 실행도 가능하다.<br><br><br>

# 컴퓨터의 구성요소
* 컴퓨터의 종류는 여러개여도 구성요소는 다 비슷하다.
* `User-interface divices` : display, keyboard, mouse
* `Storage devices` : hard disk, CD/DVD, flash
* `Network adapters` : 다른 컴퓨터와 소통하기 위한 수단<br><br><br>

# CPU 구성요소
* `Datapath` : 데이터가 어떻게 연산되어 흘러가는지
* `Control` : 프로세서의 컴포넌트 컨트롤
* `Cache memory` : 작고 빠른 `SRAM`이 있어서 `SRAM`에 자주 사용하는 데이터를 저장해 놓는다.<br><br><br>

# 추상화(Abstractions)
* 복잡한 문제를 단순화해서 쉽게 풀 수 있도록 하는 것<br><br><br>

# 메모리 종류
## 휘발성 메모리 (Volatile main memory)
* 전원을 끄면 데이터가 날아가는 메모리<br><br>

## 비휘발성 메모리 (Non-volatile secondary memory)
* 전원을 꺼도 데이터가 날아가지 않는 메모리
* 마그네틱 디스크, 플래시 메모리, CD/DVD 등<br><br><br>

# 반도체 기술
* 실리콘에 기판을 새겨넣는 것이 반도체 칩을 만드는 과정
* `conductors`(도체), `insulators`(부도체), `switch`들이 잘 동작되게 하는 것<br><br><br>

# 컴퓨터의 성능
* `throughput`과 `response time`로 정의된다.<br>

## Response time
* `operation` 하나를 처리하는 데 걸리는 시간
* 짧을수록 성능이 좋다.<br>

## Throughput
* 단위시간 당 얼마나 많은 일을 할 수 있는지 나타낸 것
* 높을수록 성능이 좋다.<br>

* ❗️ `response time`은 `throughput`에 영향을 주지만 `throughput`은 `response time`에 영향을 주지 않는다. 그래서 성능은 `response time`으로 표현할 수 있다.<br>

## Execution time
* 짧을수록 성능이 좋다.

```
Performance X / Performance Y = Execution time Y / Execution time X = n
```

* `Performance`와 `Execution time`은 반비례 관계이다.(기억하기❗️)<br>

## Elapsed time
* 총 걸린 시간
* 프로세싱, 입출력, `OS` 오버헤드, 대기시간 등 컴퓨터가 실행되는 동안 모든 것을 처리하는 데 걸린 시간
* 그 시스템 전체의 성능을 정의하게 된다.<br>

## CPU time
* 어떤 일을 처리할 때 `CPU`에서 걸리는 시간
* 그래서 입출력, 다른 작업을 처리하는 데 드는 시간 등은 제외하고 계산된다.
* `user CPU time`과 `system CPU time`으로 나눌 수 있으며 프로그램마다 `CPU`와 시스템 성능에 다르게 영향 받는다.<br><br><br>

# CPU Clocking
* `CPU`의 처리 속도
* `CPU`의 각 부품들은 일정한 시간마다 동작한다. `Clock cycle`이라 함
* `Clock period` : `Clock cycle`의 길이
* `Clock frequency (rate)` : 초당 존재하는 `Clock cycle`의 개수

## 👀 시간 단위
`second, s` > `millisecond, ms` 10^-3 > `microsecond, μs` 10^-6 > `nanosecond, ns` 10^-9 > `picosecond, ps` 10^-12<br>

* ❗️ `Clock cycle time`과 `Clock rate`는 반비례 관계이다.<br><br><br>

# 출처
* [KOCW 영남대학교 최규상 교수님 공개강의](http://www.kocw.net/home/cview.do?cid=184062fa9a833237)
