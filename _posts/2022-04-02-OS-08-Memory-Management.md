---
title: OS) Memory Management
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
    - MemoryManagement
---
# 👀 논리적 주소와 물리적 주소
* 데이터가 메모리에 위치하고 있는 곳을 가리키는 주소는 논리적 주소와 물리적 주소로 나뉜다.

* `Logical address (= virtual address)`
    * 프로세스마다 독립적으로 가지는 주소 공간
    * 각 프로세스마다 0번지부터 시작
    * `CPU`가 보는 주소는 `logical address`임
    * 왜냐면 코드상의 주소가 `logical address`인데 이게 물리적 주소로 바뀐다 해서 코드상의 주소도 물리적 주소로 바뀌는 것은 아니다. 만약 바뀐다면 컴파일을 새로 해야 하는데 이러면 이상하다. 그래서 `CPU`는 논리적 주소를 참고하게 된다.

* `Physical address`
    * 메모리에 실제로 올라가는 위치

* `CPU`가 `instruction`을 수행하다가 메모리에 있는 데이터에 접근할 필요가 있으면 그 데이터가 위치하고 있는 주소를 알아야 한다.
* 이때 코드에 물리적 주소가 항상 명시되어 있어서 바로 그 위치로 가서 읽어오면 서로 편하겠지만 프로그램이 실행되는 컴퓨터와 환경에 따라 항상 같은 물리적 주소를 사용할 수 있다는 보장이 없다.
* 그래서 코드에는 논리적 주소를 사용해 각 프로세스별로 전용 주소공간을 사용할 수 있게 하고 그 논리적 주소를 이용해 메모리에 실제로 올려져 있는 위치를 참고할 수 있도록 하는 것이다.
* `CPU`는 메모리에 접근할 필요가 있으면 먼저 논리적 주소를 물리적 주소로 변환한 뒤 해당 위치에 접근해서 데이터를 읽어온다.

## 주소 바인딩(Address Binding)
* 논리적 주소를 물리적 주소로 변환하는 것
* 하지만 주소변환은 `CPU`가 하는 것이 아닌 하드웨어적으로 이루어진다.
* 주소변환이 필요할 때마다 `CPU`가 필요하면 그 때마다 `interrupt`가 일어나게 될 것이고 그건 굉장히 비효율적일 것이다.

### 주소 바인딩 시점에 따른 분류
* `Compile time binding`
    * 컴파일될 때 물리적 주소가 결정된다.
    * 시작위치가 변경되면 재컴파일
    * 컴파일러는 절대 코드(absolute code) 생성
    * 옛날에 많이 쓰다가 지금은 잘 안 쓴다.
    
* `Load time binding`
    * `Loader`의 책임하에 물리적 메모리 주소 부여
    * 프로그램이 메모리에 로드될 때 물리적 주소가 결정된다.
    * 컴파일러가 재배치 가능 코드(relocatable code)를 생성한 경우 가능
    
* `Execution time binding (= Run time binding)`
    * 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있다.
    * `CPU`가 주소를 참조할 때마다 `binding` 점검 (address mapping table)
    * 하드웨어에서 지원해야 가능하다.
    * 최근 시스템에서 사용하는 방식
    
## Memory-Management Unit (MMU)
* 주소 변환을 위한 하드웨어
* 논리적 주소를 물리적 주소로 매핑해 준다.
* 실제 물리적 주소의 시작점에서 논리적 주소의 `offset`만큼을 더해서 물리적 주소를 알려준다.
* `Limit register` : 만약 프로세스가 자기 범위를 넘어가는 위치에 있는 데이터에 접근을 요구하면 다른 프로세스의 중요 데이터에 접근하게 될 수 있다. 그래서 주소를 변환할 때 프로세스가 가질 수 있는 주소의 최대 크기를 먼저 제한해놓고 그 범위 안에서만 변환할 수 있도록 한다.<br><br>

# 메모리 로드 기법들
## Dynamic Loading
* 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 `load`하는 것
* `memory utilization`이 향상됨
* 가끔씩 사용되는 코드가 많을 때 유용하다.
    * 자주 사용되는 부분이 한정적인데 전체를 올리면 비효율적이다.
    * 예 : 오류 처리 루틴
* 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (`OS`는 라이브러리를 통해 지원 가능)

## Dynamic Linking
* `Linking`을 실행시간(execution time)`까지 미루는 기법
* `Static linking`
    * 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    * 실행 파일의 크기가 커짐
    * 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비가 생긴다.(예: `printf`함수의 라이브러리 코드)
    
* `Overlays`
    * 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
    * 이렇게 보면 `Dynamic loading`과 비슷해 보이지만 프로그래머가 수작업으로 구현해야 한다는 점에서 다르다. 작은 공간의 메모리를 사용하던 초창기 시스템에서 사용하던 방식으로 프로그래밍이 매우 복잡하다.
    * 프로세스의 크기가 메모리보다 클 때 유용
    * 운영체제의 지원 없이 사용자에 의해 구현
    
* `Dynamic linking`
    * 라이브러리가 실행시 연결된다.
    * 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 `stub`이라는 작은 코드를 둔다.
    * 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
    * 운영체제의 도움 필요
    
* `Swapping`
    * 프로세스를 일시적으로 메모리에서 `backing store(하드디스크)`로 쫓아내는 것
    * `Backing store` : 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
    * 일반적으로 중기 스케줄러(swapper)에 의해 `swap in/out`될 프로세스가 선정된다.
    * 프로세스의 우선순위에 따라 `swap`이 이루어진다.
    * `Compile time` 혹은 `load time binding`에서는 원래 메모리 위치로 `swap in`해야 함
    * `Execution time binding`에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있다.
    * `swap time`은 대부분 `transfer time`(swap되는 양에 비례하는 시간)이다.

## Allocation of Physical Memory
* 메모리는 일반적으로 두 영역으로 나뉘어 사용된다.

* `OS` 상주 영역 : `interrupt vector`와 함께 낮은 주소 영역 사용
* 사용자 프로세스 영역 : 높은 주소 영역 사용

## 사용자 프로세스 영역의 할당 방법
* `Contiguous allocation`
    * 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
    
* `Noncontiguous allocation`
    * 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있다.
    * 현대 운영체제에서 사용
    
### Contiguous Allocation
* 고정분할 (Fixed partition) 방식
    * 물리적 메모리를 몇 개의 영구적 분할로 나눔
    * 분할의 크기가 모두 동일한 방식과 서로 다른 방식 존재
    * 분할당 하나의 프로그램 적재
    * 동시에 메모리에 로드되는 프로그램의 수가 고정됨
    * 최대 수행 가능 프로그램 크기 제한
    * `internal fragmentation(내부 조각)`, `external fragmentation(외부 조각)` 발생
    
* 가변분할 (Variable partition) 방식
    * 프로그램의 크기를 고려해서 할당
    * 분할의 크기, 개수가 동적으로 변함
    * 기술적 관리 기법 필요
    * `external fragmentation(외부 조각)` 발생
    
* `External fragmentation(외부 조각)`
    * 프로그램 크기보다 분할 크기가 작을 때 생김
    * 아무 프로그램에도 배정되지 않은 빈 공간이지만 프로그램이 올라갈 수 없는 작은 공간
    
* `Internal fragmentation(내부 조각)`
    * 프로그램 크기보다 분할 크기가 글 때 생김
    * 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
    * 특정 프로그램에 배정되었지만 사용되지 않는 공간
    
* `Hole`
    * 가용 메모리 공간
    * 다양한 크기의 `hole`들이 메모리 여러 곳에 흩어져 있다.
    * 프로세스가 도착하면 수용가능한 `hole`을 할당
    * 운영체제는 다음의 정보 유지 - 할당공간, 가용공간
    
* `Dynamic Storage-Allocation Problem`
    * 가변 분할 방식에서 size = n 인 요청을 만족하는 가장 적절한 `hole`을 찾는 문제
    
    * `First-fit`
        * 사이즈가 n 이상인 것 중 최초로 찾아지는 `hole`에 할당
    
    * `Best-fit`
        * 사이즈가 n 이상인 가장 작은 `hole`을 찾아서 할당
        * `hole`들의 리스트가 크기순으로 정렬되지 않은 경우 모든 `hole` 리스트를 탐색해야 함
        * 많은 수의 아주 작은 `hole`들이 생성됨
    
    * `Wost-fit`
        * 가장 큰 `hole`에 할당
        * 모든 리스트를 탐색해야 함
        * 상대적으로 아주 큰 `hole`들이 생성됨
        
    * `First-fit`과 `Best-fit`이 `Wost-fit`보다 속도와 공간 이용률 측면에서 효과적
    
* `Compaction`
    * `external fragmentation(외부 조각)` 문제를 해결하는 한 가지 방법
    * 사용 중인 메모리 영역을 한 군데로 몰고 `hole`들을 다른 한 곳으로 몰아 큰 `block`을 만드는 것
    * 이렇게 보면 `Windows`의 디스크 조각 모음과 비슷해 보이지만 디스크 모음은 디스크에 있는 조각들을 모으는 것인데 반해 `Compaction`은 실행 중인 메모리의 조각들을 모으는 것이라는 점에서 다르다.
    * 비용이 매우 많이 든다.
    * 최소한의 메모리 이동으로 `Compaction`하는 방법이 있지만 매우 복잡하다.
    * 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다. 런타임 바인딩이 지원될 때에만 사용 가능

# Paging
* 프로세스의 `virtual memory`를 동일한 사이즈의 `page`로 나눈다.
* `virtual memory`의 내용이 `page` 단위로 비연속적으로 저장된다.
* 일부는 `backing storage`에, 일부는 `physical memory`에 저장
* 각 페이지마다 논리적 - 물리적 주소 매핑 정보(페이징 테이블)가 필요하다.

* `physical memory`를 동일한 크기의 `frame`으로 나눔
* `logical memory`를 동일 크기의 `page`로 나눔(`frame`과 같은 크기)
* 모든 가용 `frame`들을 관리
* `page table`을 사용하여 논리적 주소를 물리적 주소로 바꾼다.
* 외부조각이 발생하지 않지만 페이징한 프로세스의 마지막 조각은 페이지 크기보다 작을 수 있기 때문에 내부조각은 발생할 수 있다.

* 속도 향상을 위해 `TLB(Translation Look-aside Buffer)`라는 하드웨어를 사용한다.
    * 일종의 캐시로 논리적 - 물리적 주소 쌍을 가지고 있다.
* 페이지 갯수만큼 페이징 테이블이 필요하기 때문에 공간이 많이 필요하다.

## Two-Level Page Table
* 페이지 테이블 공간을 줄이기 위해 사용한다. (속도 향상적인 측면에서는 별 효과 없음)
* 페이지 테이블 자체를 페이지로 구성해서 페이지 안에 페이지가 또 있는 형태이다.
* 사용되지 않는 페이지 영역은 안쪽 페이지 테이블을 안 만들고 포인터를 `NULL`로 두기 때문에 공간을 절약할 수 있다.
* 주소 공간이 더 커지면 다단계 페이지 테이블을 사용할 수도 있다.

## Memory Protection
* 페이지 테이블 각 `entry`마다 `bit`를 둔다.

* `Protection bit`
    * 페이지에 대한 접근 권한 설정(read/write/read-only 등 연산에 대한 접근 권한)
    
* `Valid-invalid bit`
    * `valid` : 해당 주소의 `frame`에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함(접근 허용)
    * `invalid` : 해당 주소의 `frame`에 유효한 내용이 없음 (접근 불허)
        * 프로세스가 그 주소 부분을 사용하지 않는 경우
        * 해당 페이지가 메모리에 올라와 있지 않고 `swap area`에 있는 경우
        
## Inverted Page Table
* 페이지 갯수가 많아짐에 따라 페이지 테이블이 커지는 문제를 줄여보려고 사용하는 테이블
* 프로세스별로 페이지 테이블을 두는 것이 아닌 페이지 프레임 하나당 페이지 테이블에 하나의 `entry`를 둔 것
* 각 `page table entry`는 각각의 물리적 메모리의 `page frame`이 담고 있는 내용 표시
* 물리적 주소를 보고 논리적 주소를 알 수 있는 테이블이라 할 수 있다.

# Shared Page
* `Shared code`
    * 공유가 가능한 코드는 `read-only`로 한 `frame`에만 올리는 것
    * 모든 프로세스의 `logical address space`에서 동일한 위치에 있어야 함

* `Private code and data`
    * 각 프로세스들은 독자적으로 메모리에 올림
    * `private data`는 `logical address space` 아무 곳에 와도 무방함
    
# Segmentation
* 프로그램은 의미 단위인 여러 개의 `segment`로 구성
* 작게는 프로그램을 구성하는 함수 하나하나에서 크게는 프로그램 전체까지도 가능
* 일반적으로는 `code`, `data`, `stack` 부분이 하나씩의 세그먼트로 정의됨
    * `main()`, `function`, `global variables`, `stack`, `symbol table`, `arrays`

* 논리적 주소는 `segment-number, offest` 쌍으로 구성된다.
* 세그먼트별 주소 변환 정보를 담고 있는 `segment table`이 필요하다.
* `segment-number`로부터 `offest`만큼 떨어진 위치에 접근하는 방식
* 각 세그먼트별로 `protection bit` 설정하기가 용이하다.
* 의미 단위이기 때문에 공유와 보안에 있어 페이징 기법보다 훨씬 효과적이다.(페이징은 한 페이지 안에 한 종류만 들어있지 않을 수 있기 때문에 종류별로 보안 설정을 다르게 해 줘야 한다)
* 페이징 기법보다는 테이블 크기가 작다.
* 외부조각 발생
* 할당은 `first fit / best fit` 방식으로 이루어지며 세그먼트의 길이가 동일하지 않기 때문에 가변분할에서와 동일한 문제점이 발생한다.

# Segmentation with Paging
* 세그먼트 하나가 여러개의 페이지로 구성된다. 둘을 섞은 것
* 훨씬 효율적이라 많이 쓰는 방식이다. 
* 메모리에 올라갈 땐 페이지 단위로 올라가고 공유는 세그먼트 단위에서 이루어진다.<br><br><br>

# 출처
* [운영체제 - 이화여자대학교 KOCW 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
