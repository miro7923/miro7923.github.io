---
title: 디자인패턴 10) 상태 패턴
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Design Pattern
tags:
    - DesignPattern
    - StatePattern
    - study
---

# CHAPTER 10. 상태 패턴(State Pattern)
## 1. 상태 패턴
* 객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.<br><br>

## 2. 상태 패턴을 사용하는 이유
* 상태 객체에 일련의 행동이 캡슐화된다. 상황에 따라 `Context` 객체에서 여러 상태 객체 중 한 객체에게 모든 행동을 맡기게 된다.
* 그 객체의 내부 상태에 따라 현재 상태를 나타내는 객체가 바뀌게 되고, 그 결과로 `Context` 객체의 행동도 자연스럽게 바뀌게 되기 때문에 클라이언트는 상태 객체를 몰라도 된다.
* `Context` 객체 내부에 조건문으로 어떤 상태에서는 어떤 행동을 해라고 일일이 써줄 필요 없이 상태 객체를 호출하기만 하면 되는 것이다.<br><br>

## 3. 상태 패턴 흐름
* `State` 인터페이스로 모든 구상 상태 클래스의 공통 인터페이스를 정의한다. 이걸 구현하는 상태 클래스들을 만든다.
* `Context` 클래스에 여러 상태 정보가 들어있다. 여기서 `request()` 메서드가 호출되면 상태 객체에게 해당 작업이 맡겨진다.
* 상태 객체에서 작업을 처리한다. <br><br>

## 4. 상태 패턴 구현 예제 - 기본

```java
public class GumballMachine {

    final static int SOLD_OUT = 0;
    final static int NO_QUARTER = 1;
    final static int HAS_QUARTER = 2;
    final static int SOLD = 3;

    int state = SOLD_OUT; // 기본값은 매진 상태
    int count = 0;

    public GumballMachine(int count) {
        this.count = count;
        if (count > 0) {
            // 알맹이가 있으면 동전 넣어주길 기다림
            state = NO_QUARTER;
        }
    }

    // 동전 투입
    public void insertQuarter() {
        if (state == HAS_QUARTER) {
            System.out.println("동전은 한 개만 넣어주세요.");
        }
        else if (state == NO_QUARTER) {
            state = HAS_QUARTER;
            System.out.println("동전을 넣으셨습니다.");
        }
        else if (state == SOLD_OUT) {
            System.out.println("매진되었습니다. 다음 기회에 이용해 주세요.");
        }
        else if (state == SOLD) {
            System.out.println("알맹이를 내보내고 있습니다.");
        }
    }

    // 동전 반환
    public void ejectQuarter() {
        if (state == HAS_QUARTER) {
            System.out.println("동전이 반환됩니다.");
            state = NO_QUARTER;
        }
        else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        }
        else if (state == SOLD) {
            System.out.println("이미 알맹이를 뽑으셨습니다.");
        }
        else if (state == SOLD_OUT) {
            System.out.println("동전을 넣지 않으셨습니다. 동전이 반환되지 않습니다.");
        }
    }

    // 손잡이를 돌리는 경우
    public void turnCrank() {
        if (state == SOLD) {
            System.out.println("손잡이는 한 번만 돌려주세요.");
        }
        else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        }
        else if (state == SOLD_OUT) {
            System.out.println("매진되었습니다.");
        }
        else if (state == HAS_QUARTER) {
            System.out.println("손잡이를 돌리셨습니다.");
            state = SOLD;
            dispense();
        }
    }

    // 알맹이 내보내기
    public void dispense() {
        if (state == SOLD) {
            System.out.println("알맹이를 내보내고 있습니다.");
            count--;
            if (count == 0) {
                System.out.println("더 이상 알맹이가 없습니다.");
                state = SOLD_OUT;
            }
            else {
                state = NO_QUARTER;
            }
        }
        else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        }
        else if (state == SOLD_OUT) {
            System.out.println("매진입니다.");
        }
        else if (state == HAS_QUARTER) {
            System.out.println("알맹이를 내보낼 수 없습니다.");
        }
    }
}
```

* 알아볼 수 있긴 하지만 코드가 간결하지 않다. 어떤 상태가 추가될 때마다 새로운 조건문을 모든 메서드에 작성해줘야 한다. 이 과정에서 버그 생길 가능성 증가 <br><br>

## 5. 상태 클래스로 구현

```java
public class GumballMachine {

    State soldOutState;
    State noQuarterState;
    State hasQuarterState;
    State soldState;

    State state = soldOutState; // 기본값은 매진 상태
    int count = 0;

    public GumballMachine(int numberGumballs) {
        soldOutState = new SoldOutState(this);
        noQuarterState = new NoQuarterState(this);
        hasQuarterState = new HasQuarterState(this);
        soldState = new SoldState(this);

        this.count = numberGumballs;
        if (numberGumballs > 0) {
            // 알맹이가 있으면 동전 넣어주길 기다림
            state = noQuarterState;
        }
        else {
            // 없으면 매진 상태
            state = soldOutState;
        }
    }

    // 동전 투입
    public void insertQuarter() {
        state.insertQuarter();
    }

    // 동전 반환
    public void ejectQuarter() {
        state.ejectQuarter();
    }

    // 손잡이를 돌리는 경우
    public void turnCrank() {
        state.turnCrank();
        state.dispense();
    }

    void setState(State state) {
        this.state = state;
    }

    void releaseBall() {
        System.out.println("알맹이를 내보내고 있습니다.");
        if (count > 0) {
            count--;
        }
    }

    public State getHasQuarterState() {
        return this.hasQuarterState;
    }

    public State getNoQuarterState() {
        return this.noQuarterState;
    }

    public State getSoldState() {
        return this.soldState;
    }

    public int getCount() {
        return this.count;
    }

    public State getSoldOutState() {
        return this.soldOutState;
    }
}
```

* 클라이언트에서는 상태 조건을 신경쓸 필요 없이 상태 객체를 호출하기만 하면 된다. 

```java
public class SoldState implements State {

    GumballMachine gumballMachine;

    public SoldState(GumballMachine gumballMachine) {
        this.gumballMachine = gumballMachine;
    }

    @Override
    public void insertQuarter() {
        System.out.println("알맹이를 내보내고 있습니다.");
    }

    @Override
    public void ejectQuarter() {
        System.out.println("이미 알맹이를 뽑으셨습니다.");
    }

    @Override
    public void turnCrank() {
        System.out.println("손잡이는 한 번만 돌려주세요.");
    }

    @Override
    public void dispense() {
        // 사용자가 동전을 넣고 손잡이를 돌리면 알맹이를 내보낸 다음
        gumballMachine.releaseBall();

        // 머신에 남은 현재 알맹이 개수에 따라 상태 변경
        if (gumballMachine.getCount() > 0) {
            gumballMachine.setState(gumballMachine.getNoQuarterState());
        }
        else {
            System.out.println("Oops, out of gumballs!");
            gumballMachine.setState(gumballMachine.getSoldOutState());
        }
    }
}
```

* 각 상태 클래스에서 인터페이스별로 행동을 구현해주면 된다.<br><br><br>

# 참고
* 헤드퍼스트 디자인패턴(2022 개정판)
