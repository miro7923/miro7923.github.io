---
title: 디자인패턴 6) 데코레이터 패턴
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Design Pattern
tags:
    - DesignPattern
    - DecoratorPattern
    - study
---

# CHAPTER 06. 커맨드 패턴(Command Pattern)
## 1. 커맨드 패턴
* 커맨드 패턴은 어떤 것을 요청하는 객체와 그 요청을 받아들이고 처리하는 객체를 분리하는 객체지향 디자인 패턴의 한 모델이다.
* 커맨드 패턴을 사용하면 요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화할 수 있다. 이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있다.<br><br>

## 2. 커맨드 패턴을 사용하는 이유
* 바뀌는 부분은 캡슐화한다.
* 상속보다는 구성을 활용한다.
* 구현보다는 인터페이스에 맞춰서 프로그래밍한다.
* 상호작용하는 객체 사이에는 가능하면 느슨한 결합을 사용해야 한다.
* 클래스는 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다(OCP).
* 추상화된 것에 의존하게 만들고 구상 클래스에 의존하지 않게 만든다.
* 위 객체지향 원칙들을 지킬 수 있다. <br><br>

## 3. 커맨드 패턴 흐름
* 요청하는 클라이언트가 커맨드 객체 생성
    * 커맨드 객체는 리시버에 전달할 일련의 행동으로 구성된다.
    * 커맨드 객체에는 행동과 리시버(Receiver)의 정보가 같이 들어있다.
    * 커맨드 객체에는 `execute()` 메서드 하나만 있는데 여기서 리시버에 있는 특정 행동을 처리하는 메서드를 호출할 수 있다.
* 클라이언트는 인보커(Invoker) 객체의 `setCommand()` 메소드를 호출해서 인보커에 커맨드 객체를 저장
* 나중에 클라이언트에서 인보커에게 그 명령을 실행하라고 요청
* 인보커에서 아까 받은 커맨드 객체의 `execute()` 메서드를 실행해서 리시버에 있는 특정 행동 메서드가 호출되게 한다.<br><br>

## 4. 커맨드 패턴 구현 예제 - 리모컨 API 만들기
### 4-1. 커맨드 인터페이스 구현
* 커맨드 객체는 모두 같은 인터페이스를 구현해야 한다. 그 인터페이스에는 `execute()`라는 이름을 가진 메서드 하나만 있다.

```java
public interface Command {
    public void execute();
}
```

### 4-2. 조명을 켤 때 필요한 커맨드 클래스 구현
* `Command` 인터페이스를 구현하는 커맨드 클래스를 만든다.

```java
public class LightOnCommand implements Command {
    Light light;

    // 생성자에 이 커맨드 객체로 제어할 특정 조명('거실조명', '부엌조명'과 같이 어느 곳에 있는 조명인지)의 정보 전달
    public LightOnCommand(Light light) {
        this.light = light;
    }

    // Light 객체에 있는 on() 메서드 호출 
    public void execute() {
        light.on();
    }
}
```

* `Light` 클래스

```java
public class Light {
    public void on() {
        System.out.println("조명이 켜졌습니다.");
    }
    
    public void off() {
        System.out.println("조명이 꺼졌습니다.");
    }
}
```

### 4-3. 커맨드 객체 사용
* 제어할 기기를 연결할 슬롯과 버튼이 각각 하나씩밖에 없는 리모컨에 커맨드 객체를 사용한다.

```java
public class SimpleRemoteControl {
    Command slot; // 리모컨 버튼과 연결되어 제어할 기기 정보를 담은 슬롯
    public SimpleRemoteControl() {}

    // 슬롯으로 제어할 명령을 설정하는 메서드
    // 리모컨 버튼의 기능을 바꾸고 싶으면 여기에 넣어주는 커맨드 객체를 바꾸면 된다.
    public void setCommand(Command command) {
        slot = command;
    }

    // 버튼이 눌리면 커맨드 객체의 execute() 함수가 실행된다.
    // execute() 메서드는 연결된 리시버 객체의 행동 메서드를 호출해 동작을 실행한다.
    public void buttonWasPressed() {
        slot.execute();
    }
}
```

* 테스트

```java
// RemoteControlTest: 커맨드 패턴에서의 클라이언트
public class RemoteControlTest {
    public static void main(String[] args) {
        // 인보커 역할
        // 필요한 작업을 요청할 때 사용할 커맨드 객체를 전달받는다.
        SimpleRemoteControl remote = new SimpleRemoteControl();

        // 요청을 받아서 처리할 리시버
        Light light = new Light();

        // 커맨드 객체. 생성시 리시버를 전달해 준다.
        LightOnCommand lightOn = new LightOnCommand(light);

        remote.setCommand(lightOn);
        remote.buttonWasPressed();
    }
}
```

<p align="center"><img src="commandPattern1.png" width="500"></p>

* 테스트 코드를 실행하면 위와 같이 조명을 켜는 `on()` 메서드가 실행되는 것을 확인할 수 있다.

#### 차고 문 여는 기능 추가
* GarageDoor(리시버) 클래스

```java
public class GarageDoor {
    public void up() {
        System.out.println("차고 문이 열렸습니다.");
    }

    public void down() {
        System.out.println("차고 문이 닫힙니다.");
    }

    public void stop() {
        System.out.println("문열기 중단");
    }

    public void lightOn() {
        System.out.println("차고 조명이 켜졌습니다.");
    }

    public void lightOff() {
        System.out.println("차고 조명이 꺼졌습니다.");
    }
}
```

* 커맨드 클래스

```java
public class GarageDoorOpenCommand implements Command {
    GarageDoor garageDoor;

    public GarageDoorOpenCommand(GarageDoor garageDoor) {
        this.garageDoor = garageDoor;
    }

    // 차고 문을 여는 커맨드니까 GarageDoor 중에서 up() 메서드를 호출한다.
    @Override
    public void execute() {
        garageDoor.up();
    }
}
```

* 테스트 코드 실행

```java
// RemoteControlTest: 커맨드 패턴에서의 클라이언트
public class RemoteControlTest {
    public static void main(String[] args) {
        // 인보커 역할
        // 필요한 작업을 요청할 때 사용할 커맨드 객체를 전달받는다.
        SimpleRemoteControl remote = new SimpleRemoteControl();

        // 요청을 받아서 처리할 리시버
        Light light = new Light();
        GarageDoor garageDoor = new GarageDoor();

        // 커맨드 객체. 생성시 리시버를 전달해 준다.
        LightOnCommand lightOn = new LightOnCommand(light);
        GarageDoorOpenCommand garageOpen = new GarageDoorOpenCommand(garageDoor);

        remote.setCommand(lightOn);
        remote.buttonWasPressed();

        remote.setCommand(garageOpen);
        remote.buttonWasPressed();
    }
}
```

<p align="center"><img src="commandPattern2.png" width="500"></p>

* 리모컨에서는 '조명켜기'와 '차고 문 열기'와 같은 특정 인터페이스가 구현되어 있다면 그 커맨드 객체에서 실제로 어떤 일을 하는지 몰라도 해당 동작을 실행시킬 수 있다. <br><br>

## 5. 커맨드 패턴의 활용
### 작업 큐
* 커맨드로 컴퓨테이션(Computation)의 한 부분(리시버와 일련의 행동)을 패키지로 묶어서 일급 객체 형태로 전달할 수 있다. 그러면 클라이언트 애플리케이션에서 커맨드 객체를 생성한 뒤 오랜 시간이 지나도 그 컴퓨테이션을 호출할 수 있다.(다른 스레드에서도 호출 가능) 이점을 활용해서 커맨드 패턴을 스케줄러나 스레드 풀, 작업 큐와 같은 다양한 작업에 적용할 수 있다.
* 작업 큐를 예로 들면, 작업하고자 하는 커맨드들이 큐에서 대기하고 있다. 각 스레드는 큐에서 커맨드를 꺼내 작업을 처리하고 나면 그 커맨드는 버리고 새로운 커맨드를 꺼내서 다음 작업을 처리하면 된다. 커맨드 패턴을 적용하면 `execute()`를 호출하는 클라이언트단에서는 해당 작업의 속성(자료형)을 신경쓸 필요가 없기 때문에 작업의 속성에 따라 새로운 스레드 객체를 생성할 필요 없이 그냥 커맨드 객체를 받아서 `execute()` 메서드를 호출하기만 하면 된다. 

### 애플리케이션 행동 복구
* 커맨드 패턴을 사용해 애플리케이션이 다운되었다가 다시 실행되었을 때 다운 전에 수행하고 있던 작업들을 복구할 수 있다. 
* `store()` 메서드를 사용해 실행 히스토리를 기록하고 애플리케이션이 다운되었다 다시 실행되면 `load()` 메서드를 사용해 저장해 뒀던 히스토리대로 `execute()` 메서드를 실행한다.<br><br><br>

# 참고
* 헤드퍼스트 디자인패턴(2022 개정판)
