---
title: OS) Virtual Memory
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Operating System
tags:
    - CS
    - OS
    - VirtualMemory
---
# 👀 Demain Paging
* 실제로 필요할 때 `page`를 메모리에 올리는 것
* 왜냐면 프로그램의 대부분의 코드는 (거의 발생하지 않는 치명적인) 오류를 해결하기 위한 코드라 평소에는 쓰지지 않는 부분이 대다수다. 그래서 이걸 다 메모리에 올려 놓으면 메모리 공간만 차지하고 비효율적이다.
* 그래서 프로세스 동작에 실제로 필요한 `page`만 메모리에 올리는 것이 효율적이다.
* 장점
    * `I/O` 양의 감소
    * 메모리 사용량 감소
    * 빠른 응답 시간
    * 더 많은 사용자 수용
<br><br>

* 물리적 메모리에 `page`가 올려질 `page entry`에서 `Valid/Invalid bit`를 사용해 현재 메모리에 `page`가 올려져 확인한다. 만약 어떤 메모리 주소에 접근했을 때 해당 위치가 `Invalid bit`로 세팅되어 있으면 `page fault`가 일어났다고 한다.
* `page fault`가 일어나면 디스크에 직접 접근해서 해당 페이지를 가져와야 하는데 이는 메모리에 접근해서 가져오는 것에 비해 시간이 아주 많이 걸린다.<br><br>

# Replacement Algorithm
## Page replacement
* 새로운 페이지를 올릴 공간이 없으면 올려져 있는 페이지 중 어떤 것을 쫓아낼 지 결정해야 한다.
* 이를 위한 여러 알고리즘이 있는데 대체로 `page fault`를 최소화하기 위해 곧바로 사용되지 않을 페이지를 쫓아내는 형태로 구현되어 있다.
    
## Optimal Algorithm
* 가장 먼 미래에 사용될(당장 사용되지 않을) 페이지를 쫓아내는 알고리즘
* 미래에 참조될 페이지를 미리 예측할 수 있다는 가정하에 사용할 수 있는 알고리즘이다. 하지만 미래를 아는 것은 불가능하기 때문에 실제 시스템에 사용은 불가능하다.

## FIFO (First In First Out) Algorithm
* 먼저 들어온 것을 먼저 내쫓는 알고리즘
* 실제 시스템에 사용되는 알고리즘이다.
* 먼저 들어온 것을 내쫓는 과정에서 페이지 프레임 크기를 늘렸는데 오히려 `page fault`가 늘어나 성능이 하락하는 경우가 생길 수 있다.

## LRU (Least Recently Used) Algorithm
* 가장 오래 전에 참조된 것을 지우는 알고리즘
* 실제 시스템에서 많이 사용된다.
* 어떤 페이지가 참조되면 그것의 순서를 맨 앞으로 바꿔주기만 하면 되기 때문에 링크드 리스트(Linked list) 형태로 구현한다. 시간복잡도는 `O(1)`

## LFU (Least Frequently Used) Algorithm
* 참조 횟수(reference count)가 가장 적은 페이지를 지우는 알고리즘
    * 최저 참조 횟수 페이지가 여러 개 있는 경우
        * 내쫓을 페이지를 임의로 선정한다.
        * 성능 향상을 위해 가장 오래 전에 참조된 페이지를 지우게 구현할 수도 있다.
    * 장단점
        * LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 페이지의 인기도를 좀 더 정확히 반영할 수 있다.
        * 참조 시점의 최근성을 반영하지 못 한다.(만약 1초 전에 처음으로 참조 되었다면 인기도가 낮은 것으로 간주하고 내쫓게 된다)
        * LRU보다 구현이 복잡하다.
* 어떤 페이지가 참조되면 이전에 참조된 페이지들의 참조 횟수와 하나씩 비교해서 순서를 정해줘야 한다. 그래서 LRU처럼 링크드 리스트 형태로 구현하면 순서를 재설정 하는 데 너무 오래 걸린다. 그래서 최소힙(Min heap) 형태로 구현한다. 참조 횟수가 가장 적은 페이지가 힙의 최상단에 있는 것이다. 시간복잡도는 `O(log n)`<br><br>

# 캐슁 기법
* 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식
* `paging system` 외에도 `cache memory`, `buffer caching`, `Web caching` 등 다양한 분야에서 사용된다.
* 그런데 `paging system`에서는 `page fault`가 생겨서 디스크에서 페이지를 가져와야 할 때에만 `OS`가 관여하고 페이지가 메모리에 있어서 디스크까지 갈 필요가 없을 때엔 `OS`가 관여하지 않는다. 그래서 `OS`는 페이지의 메모리 입장 시간 정도만 알고 있지 메모리에 올라간 이후의 참조 시간과 빈도 같은 것을 알 수 없다. 그래서 사실 위에서 설명했던 LRU와 LFU 같은 알고리즘들은 `OS`의 `paging system`에서는 사용할 수 없다.

## Clock Algorithm
* 그 대신 비슷한 효과를 낼 수 있는 `Clock Algorithm`을 사용한다.
* 시계에서 초침이 돌아가는 것처럼 포인터가 `page entry`를 돌면서 교체할 페이지를 선정하는 것이다.
* 한 바퀴 돌면서 `Reference bit`가 1이면 그동안 사용된 것으로 보고 0으로 바꾸고 넘어가고 0이면 그동안 사용하지 않은 것으로 간주해 쫓아낸다.<br><br>

# Page Frame의 Allocation
* 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지를 동시에 참조해야 하는 경우가 생길 수 있다. 이때 명령어 수행을 위해 최소한 할당되어야 하는 프레임의 수가 있는데 이 동작 `Loop`를 구성하는 페이지들은 한꺼번에 `allocate`되는 것이 유리하다. 만약 하지 않으면 매 `Loop`마다 `page fault`가 생겨 매번 디스크에 페이지를 찾으러 가야 하기 때문에 비효율적이고 성능이 떨어질 것이다.

## Allocation Scheme
* `Equal allocation` : 모든 프로세스에 똑같은 개수 할당
* `Proportional allocation` : 프로세스 크기에 비례하여 할당
* `Priority allocation` : 프로세스의 우선순위에 따라 다르게 할당

## Global replacement
* 프레임을 얻기 위해 경쟁하는 형태라 할 수 있다.
* `replace`시 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있다.
* `FIFO`, `LRU`, `LFU` 등의 알고리즘을 `global replacement`로 사용시에 해당
* `Working set`, `PFF` 알고리즘 사용

## Local replacement
* 미리 할당해 놓는 형태이다.
* 자신에게 할당된 프레임 내에서만 교체
* `FIFO`, `LRU`, `LFU` 등의 알고리즘을 프로세스별로 운영시 해당 <br><br>

# Thrashing
* 프로세스의 원활한 수행에 필요한 최소한의 `page frame`수를 할당 받지 못한 경우 발생
* `page fault rate`이 매우 높아진다.
* `CPU` 사용률이 낮아짐
* `OS`는 `MPD (Multiprogramming degree)`를 높여야 한다고 판단
* 또 다른 프로세스가 시스템에 추가됨(higher MPD)
* 프로세스 당 할당된 프레임 수가 더욱 감소된다.
* 프로세스는 페이지의 `swap in / swap out`으로 바쁘다. 그래서 멈춰 있는 것처럼 보인다. (정상적인 동작 불가)
* 그래서 대부분의 시간에 `CPU`는 한가하다.(log throughput)
* 동시에 메모리에 올라가 있는 프로그램의 수 조절이 필요하다. 이를 위해 사용되는 것이 `Working-set model`이다.

## Working-Set Model
* 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다.
* 집중적으로 참조되는 해당 페이지들의 집합을 `localityy set`이라 한다.
* `locality`에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 페이지들의 집합을 `Working Set`이라 정의한다.
* `Working Set` 모델에서는 프로세스의 `Working Set` 전체가 메모리에 올라와 있어야 수행되고 그렇지 않고 부분적으로 있으면 가지고 있던 프레임도 모두 반납 후 `swap out(suspend)`된다. 이를 통해 `thrashing`을 어느 정도 방지할 수 있다.
* `Working Set`을 제대로 탐지하기 위해서는 `window size`를 잘 결정해야 한다. 너무 작으면 `localiry set`을 모두 수용하지 못할 수 있고, 너무 크면 여러 규모의 `locality set`을 수용하게 될 것이다. 

## PFF (Page-Fault Frequency) Scheme
* `page fault rate`의 상한값과 하한값을 둔다.
* `page fault`가 많을수록 많은 페이지 프레임을 할당하고 그렇지 않으면 할당된 프레임 수를 줄인다.
* 빈 프레임이 없으면 일부 프로세스를 `swap out` 시킨다.<br><br>

# Page Size의 결정
* 페이지의 크기가 너무 작으면 페이지 수가 증가하게 되어 페이지 테이블의 크기가 증가할 것이다. 
* 작게 쪼개진만큼 너무 필요한 정보만 메모리에 올라와 있게 되어 `page fault`의 빈도수가 늘어나 디스크에 접근해야 하는 시간이 늘어날 것이다. 이런 측면에서 볼 때 메모리 이용은 효율적이지만 `locality` 활용 측면에서는 좋지 않다.<br><br><br>

# 출처
* [운영체제 - 이화여자대학교 KOCW 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
* [스레싱(thrashing)이란 무엇인가](https://straw961030.tistory.com/155)
