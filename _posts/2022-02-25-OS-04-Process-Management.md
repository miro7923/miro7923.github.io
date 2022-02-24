---
title: OS) Process Management
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
---
# 👀 프로세스 생성 (Process Creation)
* 부모 프로세스(Parent process)가 자식 프로세스(Child process) 생성. 복제 생성 하는 것으로 부모 프로세스의 문맥(코드, 데이터, 스택 등)을 모두 복사한다.
* 복제된 자식 프로세스가 부모 프로세스의 하위 노드로 위치하고 그 자식이 또 자식을 복제하면 또 하위 노드로 위치하고... 를 반복하면서 프로세스의 트리(계층 구조)를 형성한다.
* 프로세스 혼자서 자식을 생성할 수는 없고 시스템 콜을 통해 운영체제의 서비스를 받아야만 자식 생성이 가능하다.
* 프로세스는 자원을 필요로 하기 때문에 운영체제로부터 받거나 부모와 공유한다. 기본적으로는 자식이 복제되는 순간 별도의 프로세스가 되기 때문에 그 순간부터 부모와 자원을 얻기 위해 경쟁하는 관계가 된다.

## 자원의 공유
* 부모와 자식이 모든 자원을 공유하는 모델
* 일부를 공유하는 모델
* 전혀 공유하지 않는 모델 (일반적)

## 수행 (Execution)
* 부모와 자식은 공존하며 수행되는 모델
* 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델

## 주소 공간 (Address space)
* 자식은 부모의 공간을 복사함 (binary and OS data)
* 자식은 그 공간에 새로운 프로그램을 올린다.(덮어씌움)

### 유닉스의 예
* `fork()` 시스템 콜이 새로운 프로세스를 생성
    * 부모를 그대로 복사 후 주소 공간 할당
* `fork` 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올림 (덮어씌우는 단계)
* `fork`와 `exec`은 독립적으로 이루어지기 때문에 둘 중 하나만 실행될 수 있다.<br><br><br>

# 프로세스 종료 (Process Termination)
* 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다.
* 그래서 단계적인 종료를 통해 모든 자식 프로세스가 먼저 종료된 후 부모가 종료되어야 한다.

## System call을 통해 자발적으로 종료되는 경우
* 프로세스가 마지막 명령을 수행한 후 이를 운영체제에게 알려준다.(`exit`)
* 자식이 부모에게 output data를 보냄 (via `wait`)
* 프로세스의 각종 자원들이 운영체제에게 반납됨

## 비자발적으로 종료되는 경우
* 부모 프로세스가 자식 프로세스의 수행을 강제로 종료시킨다.(`abort`)
* 자식이 할당 자원의 한계치를 넘어선 경우
* 자식에게 할당된 태스크가 더 이상 필요하지 않은 경우
* 부모가 종료(exit)하는 경우<br><br><br>

# fork() 시스템 콜

```c
int main()
{
    int pid; // 부모와 자식을 구분하기 위한 값
    pid = fork(); // 새로운 자식 프로세스가 생성되면 여기 다음 줄부터 실행한다.(부모의 문맥을 복사하기 때문)
    if (0 == pid)   // this is a child
        printf("\n Hello, I am a child!\n");
    else if (0 < pid)   // this is a parent
        printf("\n Hello, I am a parent!\n");
}
```

* 자식 프로세스를 생성하기 위한 시스템 콜로 부모와 자식을 구분하기 위해 부모의 `pid`는 0보다 큰 값을 가지고 자식의 `pid`는 0을 가진다.<br><br><br>

# exec() 시스템 콜

```c
int main()
{
    int pid;
    pid = fork();
    if (0 == pid)   // this is a child
    {
        printf("\n Hello, I am a child! Now I'll run date \n");
        execlp("/bin/date", "/bin/date", (char *)0);
    }
    else if (0 < pid)   // this is a parent
        printf("\n Hello, I am a parent!\n");
}
```

* 어떤 프로그램을 새로운 프로세스로 바꿔주기 때문에 `execlp()`가 호출되는 순간 이후의 코드들은 실행되지 않는다.<br><br><br>

# wait() 시스템 콜

```c
int main()
{
    int childPID;
    S1;
    
    childPID = fork();
    
    if (0 == childPID)
        // child 프로세스 코드 실행
    else 
        wait();
        
    S2;
}
```

* 프로세스 A가 `wait()` 시스템 콜을 호출하면 커널은 `child`가 종료될 때까지 프로세스 A를 `sleep`시킨다.(block 상태)
* 자식 프로세스가 종료되면 커널은 프로세스 A를 깨운다.(ready 상태)<br><br><br>

# exit() 시스템 콜
## 프로세스의 자발적 종료
* 마지막 statement 수행 후 `exit()` 시스템 콜을 통해 종료
* 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌

## 프로세스의 비자발적 종료
* 부모 프로세스가 자식 프로세스를 강제 종료시킴
    * 자식 프로세스가 한계치를 넘어서는 자원 요청시
    * 자식에게 할당된 태스크가 더 이상 필요하지 않을 때
* 키보드로 `kill`, `break`등을 친 경우
* 부모가 종료하는 경우 부모 프로세스가 종료되기 전 자식 프로세스들이 먼저 종료된다.<br><br><br>

# 프로세스 간 협력
## 독립적 프로세스 (Independent process)
* 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

## 협력 프로세스 (Cooperating process)
* 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.

## 프로세스 간 협력 메커니즘 (`IPC`: Interprocess Communication)
### 🔸 메시지를 전달하는 방법
* `message passing` : 커널을 통해 메시지 전달

#### 🔸 주소 공간을 공유하는 방법
* `shared memory` : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
* 물리적 메모리에 매핑할 때 일부 주소공간을 공유하도록 매핑한다.(사전에 시스템 콜을 통해 운영체제에게 보고되어야 한다)

#### 🔸 thread
* `thread`는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 스레드들 간에는 주소 공간을 공유하기 때문에 협력이 가능하다. <br><br><br>

# Message Passing
* 사용자 프로그램끼리는 메시지를 주고받는 것이 불가능하기 때문에 운영체제 커널을 통해서 메시지를 주고받는다.

## Message system
* 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템

## Direct Communication
* 통신하려는 프로세스의 이름을 명시적으로 표시해서 메시지를 주고받는 것

## Indirect Communication
* mailbox(또는 port)를 통해 메시지를 간접 전달하는 방식<br><br><br>

# 출처
* [운영체제 - 이화여자대학교 KOCW 공개강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
