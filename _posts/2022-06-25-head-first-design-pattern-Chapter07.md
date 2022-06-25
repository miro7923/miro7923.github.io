---
title: 디자인패턴 7) 어댑터 패턴과 퍼사드 패턴
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Design Pattern
tags:
    - DesignPattern
    - AdapterPattern
    - FacadePattern
    - study
---

# CHAPTER 07. 어댑터 패턴과 퍼사드 패턴(Adapter & Facade Pattern)
## 1. 어댑터 패턴
* 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환한다. 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와준다.<br><br>

## 2. 어댑터 패턴을 사용하는 이유
* 호환되지 않는 인터페이스를 사용하는 클라이언트를 그대로 활용할 수 있다.
* 클라이언트와 구현된 인터페이스를 분리할 수 있다.
* 변경 내역이 어댑터에 캡슐화되기 때문에 나중에 인터페이스가 바뀌더라도 클라이언트를 바꿀 필요가 없다.<br><br>

## 3. 어댑터 패턴 흐름
* 클라이언트는 타깃 인터페이스에 맞게 구현되어 있다.
    * 클라이언트에서 타깃 인터페이스로 메소드를 호출해서 어댑터에 요청을 보낸다.
* 어댑터는 타깃 인터페이스를 구현하며, 여기에는 어댑티(Adaptee) 인스턴스가 들어있다.
    * 어댑터는 어댑티 인터페이스로 그 요청을 어댑티에 관한 (하나 이상의) 메소드 호출로 변환한다.
* 클라이언트는 호출 결과를 받긴 하지만 중간에 어댑터가 있다는 사실을 모른다.<br><br>

## 4. 객체 어댑터와 클래스 어댑터
* 지금까지 배운 것은 객체 어댑터이고 다중 상속을 이용한 클래스 어댑터가 있다.
* 자바에서는 다중 상속을 지원하지 않기 때문에 클래스 어댑터의 구현이 가능하지 않다.
* 클래스 어댑터 구현 : 어댑터를 어댑티와 타깃 클래스의 서브클래스로 만든다. (어댑티 클래스랑 타깃 클래스 두 개를 상속받게 함)

```cpp
class Client
{
    
};

class Target : public Client
{
public:
    void request() {}
};

class Adaptee
{
public:
    void specificRequest() {}
};

class Adapter : public Target, Adaptee
{
public:
    void request() {}
};
```

* `C++`로 구현한 클래스 어댑터<br><br>

## 5. 어댑터 패턴 구현 예제 - Iterator와 Enumeration
* [[자바/java]Iterator 와 Enumeration 쉽게 이해하기 편.](https://tiboy.tistory.com/481)

### 5-1. Enumeration을 Iterator로 변환하는 어댑터 클래스

```java
import java.util.Enumeration;
import java.util.Iterator;

public class EnumerationIterator implements Iterator<Object> {
    Enumeration<?> enumeration;

    public EnumerationIterator(Enumeration<?> enumeration) {
        this.enumeration = enumeration;
    }

    public boolean hasNext() {
        return enumeration.hasMoreElements();
    }

    public Object next() {
        return enumeration.nextElement();
    }

    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

### 5-2. Iterator를 Enumeration으로 변환하는 어댑터 클래스

```java
import java.util.Enumeration;
import java.util.Iterator;

public class IteratorEnumeration implements Enumeration<Object> {
    Iterator<?> iterator;

    public IteratorEnumeration(Iterator<?> iterator) {
        this.iterator = iterator;
    }

    @Override
    public boolean hasMoreElements() {
        return iterator.hasNext();
    }

    @Override
    public Object nextElement() {
        return iterator.next();
    }
}
```

### 5-3. 테스트

```java
import java.util.ArrayList;

public class Test {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        IteratorEnumeration iten = new IteratorEnumeration(list.iterator());
        System.out.println(iten.hasMoreElements());
        System.out.println(iten.nextElement());
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/adapterPattern1.png" width="500"></p><br><br>

## 6. 퍼사드 패턴(Facade Pattern)
* 서브시스템에 있는 일련의 인터페이스를 통합 인터페이스로 묶어 준다. 또한 고수준 인터페이스도 정의하므로 서브시스템을 더 편리하게 사용할 수 있다.
* 퍼사드 패턴을 사용하면 클라이언트와 서브시스템이 긴밀하게 연결되지 않아도 된다.
* 퍼사드 패턴을 사용하면 **최소 지식 원칙**을 지킬 수 있다.<br><br>

## 7. 퍼사드 패턴 구현 예제 - 홈시어터 만들기
* 홈시어터 퍼사드 만들기

```java
public class HomeTheaterFacade {
    Amplifier amp;
    Tuner tuner;
    StreamingPlayer player;
    Projector projector;
    TheaterLights lights;
    Screen screen;
    PopcornPopper popper;

    public HomeTheaterFacade(Amplifier amp,
                             Tuner tuner,
                             StreamingPlayer player,
                             Projector projector,
                             Screen screen,
                             TheaterLights lights,
                             PopcornPopper popper) {
        this.amp = amp;
        this.tuner = tuner;
        this.player = player;
        this.projector = projector;
        this.screen = screen;
        this.lights = lights;
        this.popper = popper;
    }

    public void watchMovie(String movie) {
        System.out.println("영화 볼 준비 중");
        popper.on();
        popper.pop();
        lights.dim(0);
        screen.down();
        projector.on();
        projector.wideScreenMode();
        amp.on();
        amp.setStreamingPlayer(player);
        amp.setSurroundSound();
        amp.setVolume(5);
        player.on();
        player.play(movie);
    }

    public void endMovie() {
        System.out.println("홈시어터 끄는 중");
        popper.off();
        lights.on();
        screen.up();
        projector.off();
        amp.off();
        player.stop();
        player.off();
    }
}
```

* 호출

```java
public class HomeTheaterTestDrive {
    public static void main(String[] args) {
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(amp, tuner, player, projector, screen, lights, popper);

        homeTheater.watchMovie("겨울왕국");
        homeTheater.endMovie();
    }
}

```

* 동작에 필요한 메서드들을 복잡하게 하나씩 호출할 필요 없이 단순화 시킨 인터페이스 하나만 호출하면 된다.<br><br>

## 8. 최소 지식 원칙
* 디자인 원칙으로 **진짜 절친에게만 이야기해야 한다**는 원칙
* 여러 클래스가 얽혀 있어서 시스템의 한 부분을 변경했을 때 다른 부분까지 줄줄이 고쳐야 하는 상황을 미리 방지할 수 있다.
* **최소 지식 원칙**대로 하면 자바 표준 입출력인 `System.out.println()`과 같은 형태로 쓰면 안 되지만 워낙 보편적으로 사용하니까 그냥 씀

### 8-1. 최소 지식 원칙을 잘 따른 예와 아닌 예
* 따르지 않은 예

```java
public float getTemp() {
    Thermometer therm = station.getThermometer();
    return therm.getTemperature();
}
```

* `station`으로부터 객체를 한 번 받은 다음에 그 객체의 메서드를 직접 호출하고 있다.

* 따른 예

```java
public float getTemp() {
    return station.getTemperature();
}
```

* `Thermometer`에게 요청을 전달하는 메서드를 `station` 클래스에 추가한 뒤 호출해서 의존해야 하는 클래스의 개수를 줄임
<br><br><br>

# 참고
* 헤드퍼스트 디자인패턴(2022 개정판)
