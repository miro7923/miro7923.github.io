---
title: 디자인패턴 12) 복합 패턴
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Design Pattern
tags:
    - DesignPattern
    - CompositePattern
    - study
---

# CHAPTER 12. 복합 패턴(Composite Pattern)
## 1. 복합 패턴
* 반복적으로 생길 수 있는 일반적인 문제를 해결하는 용도로 2개 이상의 패턴을 결합해서 사용하는 것

## 2. 복합 패턴 구현 예제 - 오리 시뮬레이션 게임
### 2-1. 오리 구상 클래스 만들기 

```java
public interface Quackable {
    public void quack(); // 꽥꽥 소리 내는 메서드
}
```

```java
public class MallardDuck implements Quackable {
    @Override
    public void quack() {
        System.out.println("꽥꽥");
    }
}

public class DuckCall implements Quackable {
    @Override
    public void quack() {
        System.out.println("꽉꽉");
    }
}
```

* 오리 소리를 내는 인터페이스와 그걸 구현한 오리 클래스들 생성

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        simulator.simulate();
    }

    void simulate() {
        Quackable mallardDuck = new MallardDuck();
        Quackable redheadDuck = new RedheadDuck();
        Quackable duckCall = new DuckCall();
        Quackable rubberDuck = new RubberDuck(); // 아까 만든 오리들 생성

        System.out.println("\n오리 시뮬레이션 게임");

        simulate(mallardDuck);
        simulate(redheadDuck);
        simulate(duckCall);
        simulate(rubberDuck);   // 오리들 꽥꽥 확인
    }

    void simulate(Quackable duck) {
        duck.quack();   // 매개변수로 받은 오리의 꽥꽥 메서드 실행 
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/compsitePattern1.png" width="300"></p>

### 2-2. 어댑터 패턴 사용해서 거위 추가 

* 거위 클래스 추가

```java
public class Goose {
    public void honk() {
        System.out.println("끽끽");
    }
}
```

* 거위를 오리와 같이 쓸 수 있게 어댑터 추가 - 변환할 타깃 인터페이스를 구현해야 함

```java
public class GooseAdapter implements Quackable {
    Goose goose;

    public GooseAdapter(Goose goose) {
        this.goose = goose;
    }
    
    @Override
    public void quack() {
        goose.honk();
    }
}
```

* 시뮬레이터에 거위도 추가해서 테스트

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        simulator.simulate();
    }

    void simulate() {
        Quackable mallardDuck = new MallardDuck();
        Quackable redheadDuck = new RedheadDuck();
        Quackable duckCall = new DuckCall();
        Quackable rubberDuck = new RubberDuck(); // 아까 만든 오리들 생성
        Quackable goose = new GooseAdapter(new Goose()); // 어댑터를 사용하면 오리로 감싼 거위 생성 가능

        System.out.println("\n오리 시뮬레이션 게임");

        simulate(mallardDuck);
        simulate(redheadDuck);
        simulate(duckCall);
        simulate(rubberDuck);   // 오리들 꽥꽥 확인
        simulate(goose);    // 거위도 꽉꽉 가능 
    }

    void simulate(Quackable duck) {
        duck.quack();
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/compsitePatter2.png" width="300"></p>

### 2-3. 데코레이터 패턴 사용해서 오리가 소리 낸 횟수 세는 기능 추가

* 오리가 소리 내는 횟수를 세는 클래스를 데코레이터 클래스로 만듦

```java
public class QuackCounter implements Quackable { // 데코레이터 클래스
    Quackable duck;
    static int duckCnt; // 모든 객체에서 꽥꽥 소리를 낸 횟수를 세야 하기 때문에 static 변수 사용

    public QuackCounter(Quackable duck) {
        this.duck = duck;
    }

    @Override
    public void quack() {
        duck.quack();
        duckCnt++;  // 꽥꽥 메서드가 호출되면 행동은 duck 객체로 위임하고 횟수 증가
    }

    public static int getDuckCnt() {
        return duckCnt;
    }
}
```

* 시뮬레이터 수정해서 한 번 더 테스트

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        simulator.simulate();
    }

    void simulate() {
        Quackable mallardDuck = new QuackCounter(new MallardDuck());
        Quackable redheadDuck = new QuackCounter(new RedheadDuck());
        Quackable duckCall = new QuackCounter(new DuckCall());
        Quackable rubberDuck = new QuackCounter(new RubberDuck()); // Quackable을 데코레이터로 감싸 줌
        Quackable goose = new GooseAdapter(new Goose()); // 어댑터를 사용하면 오리로 감싼 거위 생성 가능

        System.out.println("\n오리 시뮬레이션 게임");

        simulate(mallardDuck);
        simulate(redheadDuck);
        simulate(duckCall);
        simulate(rubberDuck);   // 오리들 꽥꽥 확인
        simulate(goose);    // 거위도 꽉꽉 가능

        System.out.println("오리가 소리 낸 횟수 : " + QuackCounter.getDuckCnt() + " 번");
    }

    void simulate(Quackable duck) {
        duck.quack();
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/compsitePatter3.png" width="300"></p>

### 2-4. 추상 팩토리 패턴 사용해서 오리들을 캡슐화하기

* 오리를 생성할 추상 클래스 정의 

```java
public abstract class AbstractDuckFactory {
    public abstract Quackable createMallardDuck();
    public abstract Quackable createRedheadDuck();
    public abstract Quackable createDuckCall();
    public abstract Quackable createRubberDuck();
    public abstract Quackable createGoose();
}
```

* 추상 클래스를 상속한 오리 생성 팩토리 생성
* 각 오리들이 소리낸 횟수를 셀 수 있게 데코레이터 클래스로 감싼다.

```java
public class CountingDuckFactory extends AbstractDuckFactory {
    @Override
    public Quackable createMallardDuck() {
        return new QuackCounter(new MallardDuck());
    }

    @Override
    public Quackable createRedheadDuck() {
        return new QuackCounter(new RedheadDuck());
    }

    @Override
    public Quackable createDuckCall() {
        return new QuackCounter(new DuckCall());
    }

    @Override
    public Quackable createRubberDuck() {
        return new QuackCounter(new RubberDuck());
    }

    @Override
    public Quackable createGoose() {
        return new GooseAdapter(new Goose());
    }
}
```

* 시뮬레이터 수정해서 테스트
* 이제 오리 팩토리를 사용해서 오리 객체를 만들 것이기 때문에 시뮬레이터에서 객체를 직접 생성하던 부분을 없애고 팩토리의 생성 메서드를 사용하는 것으로 바꾼다.

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        AbstractDuckFactory duckFactory = new CountingDuckFactory();

        simulator.simulate(duckFactory); // 오리 팩토리를 사용할 수 있게 수정
    }

    void simulate(AbstractDuckFactory duckFactory) {
        Quackable mallardDuck = duckFactory.createMallardDuck();
        Quackable redheadDuck = duckFactory.createRedheadDuck();
        Quackable duckCall = duckFactory.createDuckCall();
        Quackable rubberDuck = duckFactory.createRubberDuck(); 
        Quackable goose = duckFactory.createGoose(); // 객체의 인스턴스를 팩토리에서 생성하도록 위임

        System.out.println("\n오리 시뮬레이션 게임");

        simulate(mallardDuck);
        simulate(redheadDuck);
        simulate(duckCall);
        simulate(rubberDuck);   // 오리들 꽥꽥 확인
        simulate(goose);    // 거위도 꽉꽉 가능

        System.out.println("오리가 소리 낸 횟수 : " + QuackCounter.getDuckCnt() + " 번");
    }

    void simulate(Quackable duck) {
        duck.quack();
    }
}
```

### 2-5. 컴포지트 패턴으로 오리 무리 만들기

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Flock implements Quackable {
    List<Quackable> quackers = new ArrayList<Quackable>();

    public void add(Quackable quacker) {
        quackers.add(quacker);
    }

    @Override
    public void quack() {
        Iterator<Quackable> it = quackers.iterator(); // 반복자 패턴 사용 
        while (it.hasNext()) {
            Quackable quacker = it.next();
            quacker.quack();
        }
    }
}
```

* 시뮬레이터 수정 후 테스트

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        AbstractDuckFactory duckFactory = new CountingDuckFactory();

        simulator.simulate(duckFactory); // 오리 팩토리를 사용할 수 있게 수정
    }

    void simulate(AbstractDuckFactory duckFactory) {
        Quackable redheadDuck = duckFactory.createRedheadDuck();
        Quackable duckCall = duckFactory.createDuckCall();
        Quackable rubberDuck = duckFactory.createRubberDuck(); // 객체의 인스턴스를 팩토리에서 생성하도록 위임
        Quackable goose = duckFactory.createGoose(); // 어댑터를 사용하면 오리로 감싼 거위 생성 가능

        System.out.println("\n오리 시뮬레이션 게임 : 무리 (+컴포지트)");

        Flock flockOfDucks = new Flock();

        flockOfDucks.add(redheadDuck);
        flockOfDucks.add(duckCall);
        flockOfDucks.add(rubberDuck);
        flockOfDucks.add(goose);    // 오리 무리 생성

        Flock flockOfMallards = new Flock();

        Quackable mallardOne = duckFactory.createMallardDuck();
        Quackable mallardTwo = duckFactory.createMallardDuck();
        Quackable mallardThree = duckFactory.createMallardDuck();
        Quackable mallardFour = duckFactory.createMallardDuck();

        flockOfMallards.add(mallardOne);
        flockOfMallards.add(mallardTwo);
        flockOfMallards.add(mallardThree);
        flockOfMallards.add(mallardFour);   // 물오리 무리 생성

        flockOfDucks.add(flockOfMallards);  // 물오리 무리를 오리 무리에 추가

        System.out.println("오리 시뮬레이션 게임 : 전체 무리");
        simulate(flockOfDucks);

        System.out.println("오리 시뮬레이션 게임 : 물오리 무리");
        simulate(flockOfMallards);

        simulate(redheadDuck);
        simulate(duckCall);
        simulate(rubberDuck);   // 오리들 꽥꽥 확인
        simulate(goose);    // 거위도 꽉꽉 가능

        System.out.println("오리가 소리 낸 횟수 : " + QuackCounter.getDuckCnt() + " 번");
    }

    void simulate(Quackable duck) {
        duck.quack();
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/compsitePatter4.png" width="300"></p>

### 2-6. 옵저버 패턴으로 개별 오리를 추적하는 기능 만들기

* 옵저버 인터페이스 정의

```java
public interface QuackObservable {
    public void registerObserver(Observer observer);
    public void notifyObservers();
}

// 꽥꽥 인터페이스에서 옵저버 인터페이스 확장
public interface Quackable extends QuackObservable {
    public void quack(); // 꽥꽥 소리 내는 메서드
}
```

* 옵저버 보조 클래스 생성 - 옵저버를 등록하고 연락을 돌리는 기능 

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Observable implements QuackObservable {
    List<Observer> observers = new ArrayList<>();
    QuackObservable duck;

    public Observable(QuackObservable duck) {
        this.duck = duck;
    }

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);    // 옵저버 등록
    }

    @Override
    public void notifyObservers() {
        Iterator<Observer> it = observers.iterator();
        while (it.hasNext()) {
            Observer observer = it.next();
            observer.update(duck);  // 연락 돌림
        }
    }
}
```

* 옵저버 보조 객체와 꽥꽥 클래스 결합

```java
import java.util.Observer;

public class MallardDuck implements Quackable {
    Observable observable;

    public MallardDuck() {
        this.observable = new Observable(this);
    }

    @Override
    public void quack() {
        System.out.println("꽥꽥");
        notifyObservers();  // 꽥꽥 메서드가 호출되면 옵저버들에게 알려줌
    }

    @Override
    public void registerObserver(Observer observer) {
        this.observable.registerObserver(observer);
    }

    @Override
    public void notifyObservers() {
        this.observable.notifyObservers();
    }
}
```

* 오리 소리 세는 클래스에도 옵저버 추가

```java
import java.util.Observer;

public class QuackCounter implements Quackable { // 데코레이터 클래스
    Quackable duck;
    static int duckCnt; // 모든 객체에서 꽥꽥 소리를 낸 횟수를 세야 하기 때문에 static 변수 사용

    public QuackCounter(Quackable duck) {
        this.duck = duck;
    }

    @Override
    public void quack() {
        duck.quack();
        duckCnt++;  // 꽥꽥 메서드가 호출되면 행동은 duck 객체로 위임하고 횟수 증가
    }

    public static int getDuckCnt() {
        return duckCnt;
    }

    @Override
    public void registerObserver(Observer observer) {
        duck.registerObserver(observer);
    }

    @Override
    public void notifyObservers() {
        duck.notifyObservers();
    }
}
```

* 옵저버 인터페이스와 클래스

```java
public interface Observer {
    public void update(QuackObservable duck);
}

public class Quackologist implements Observer {
    @Override
    public void update(QuackObservable duck) {
        System.out.println("꽥꽥학자 : " + duck + "가 소리냈다.");
    }
}
```

* 시뮬레이터 수정 후 테스트

```java
public class DuckSimulator {
    public static void main(String[] args) {
        DuckSimulator simulator = new DuckSimulator();
        AbstractDuckFactory duckFactory = new CountingDuckFactory();

        simulator.simulate(duckFactory); // 오리 팩토리를 사용할 수 있게 수정
    }

    void simulate(AbstractDuckFactory duckFactory) {
        Quackable redheadDuck = duckFactory.createRedheadDuck();
        Quackable duckCall = duckFactory.createDuckCall();
        Quackable rubberDuck = duckFactory.createRubberDuck();
        Quackable gooseDuck = new GooseAdapter(new Goose());

        Flock flockOfDucks = new Flock();

        flockOfDucks.add(redheadDuck);
        flockOfDucks.add(duckCall);
        flockOfDucks.add(rubberDuck);
        flockOfDucks.add(gooseDuck);

        Flock flockOfMallards = new Flock();

        Quackable mallardOne = duckFactory.createMallardDuck();
        Quackable mallardTwo = duckFactory.createMallardDuck();
        Quackable mallardThree = duckFactory.createMallardDuck();
        Quackable mallardFour = duckFactory.createMallardDuck();

        flockOfMallards.add(mallardOne);
        flockOfMallards.add(mallardTwo);
        flockOfMallards.add(mallardThree);
        flockOfMallards.add(mallardFour);

        flockOfDucks.add(flockOfMallards);  // 물오리 무리를 오리 무리에 추가

        System.out.println("오리 시뮬레이션 게임 (+옵저버)");

        Quackologist quackologist = new Quackologist();
        flockOfDucks.registerObserver(quackologist);

        simulate(flockOfDucks);

        System.out.println("오리가 소리 낸 횟수 : " + QuackCounter.getDuckCnt() + " 번");
    }

    void simulate(Quackable duck) {
        duck.quack();
    }
}
```

<p align="center"><img src="https://github.com/Developer-book-club/headfirst-design-pattern-2205/raw/main/yujin/compsitePatter5.png" width="300"></p><br><br><br>

# 참고
* 헤드퍼스트 디자인패턴(2022 개정판)
