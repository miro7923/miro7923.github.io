---
title: 디자인패턴 3) 데코레이터 패턴
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

# CHAPTER 03. 데코레이터 패턴(Decorator Pattern)
## 1. 데코레이터 패턴

<p align="center"><img src="decoratorPattern.png" width="500"></p>

* 데코레이터 패턴은 위 그림과 같이 객체에 추가되는 요소를 하나씩 더해서 기존 객체를 감싸는 형태이다. 
* 데코레이터 패턴을 사용하면 객체에 추가 요소를 동적으로 더할 수 있고 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있다. 
* 데코레이터의 형식은 그 데코레이더로 감싸는 객체의 형식과 같아야 하기 때문에 데코레이터를 만들 때엔 상속을 통해 구현한다. 하지만 동작은 새로운 데코레이터를 만들 때에 추가하고 부모로부터 상속받지 않는다. 그래서 데코레이터가 여러 개 생겨도 유연성을 잃지 않을 수 있다.
* 하지만 데코레이터 패턴을 사용해 디자인을 하다 보면 잡다한 클래스가 많아지는 단점이 있다.<br><br>

## 2. 데코레이터 패턴을 사용하는 이유
* 클래스는 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다(OCP) 원칙을 지키기 위해서!<br><br>

## 3. 데코레이터 패턴 설계 예제
### 3-1. 커피 주문 시스템 코드 만들기
* 음료 추상 클래스 만들기

```java
public abstract class Beverage {

    String desc = "제목 없음";

    public String getDesc() {
        return desc;
    }

    public abstract double cost(); // 자식 클래스에서 구현
}
```

* 첨가물을 나타내는 추상 클래스(데코레이터 클래스) 만들기

```java
public abstract class CondimentDecorator extends Beverage {
    
    Beverage beverage; // 각 데코레이터가 감쌀 음료 
    
    public abstract String getDesc(); // 자식 클래스에서 구현
}
```

### 3-2. 음료 코드 구현
* 에스프레소 커피 클래스 만들기

```java
public class Espresso extends Beverage {
    
    public Espresso() {
        desc = "에스프레소";
    }
    
    public double cost() {
        return 1.99;
    }
}
```

* 하우스 블렌드 커피 클래스

```java
public class HouseBlend extends Beverage {
    
    public HouseBlend() {
        desc = "하우스 블렌드 커피";
    }
    
    public double cost() {
        // 하우스 블렌드 가격 리턴
        return .89;
    }
}
```

* 다크로스트 커피 클래스

```java
public class DarkRoast extends Beverage {
    
    public DarkRoast() {
        desc = "다크 로스트 커피";
    }
    
    public double cost() {
        return .99;
    }
}
```

* 디카페인 클래스

```java
public class Decaf extends Beverage {
    
    public Decaf() {
        desc = "디카페인";
    }
    
    public double cost() {
        return 1.05;
    }
}
```

### 3-3. 첨가물 코드 구현
* 모카 클래스

```java
public class Mocha extends CondimentDecorator {
    
    public Mocha(Beverage beverage) {
        // 어떤 음료에 첨가될 것인지 정한다.
        this.beverage = beverage;
    }
    
    public String getDesc() {
        return beverage.getDesc() + ", 모카";
    }
    
    public double cost() {
        // 감싼 음료의 가격에 첨가물 가격을 더한 값을 리턴 
        return beverage.cost() + .20;
    }
}
```

* 우유 클래스

```java
public class Milk extends CondimentDecorator {
    
    public Milk(Beverage beverage) {
        this.beverage = beverage;
    }
    
    public String getDesc() {
        return beverage.getDesc() + ", 우유";
    }
    
    public double cost() {
        return beverage.cost() + .10;
    }
}
```

* 두유 클래스

```java
public class SoyMilk extends CondimentDecorator {
    
    public SoyMilk(Beverage beverage) {
        this.beverage = beverage;
    }
    
    public String getDesc() {
        return beverage.getDesc() + ", 두유";
    }
    
    public double cost() {
        return beverage.cost() + .15;
    }
}
```

* 휘핑크림 클래스

```java
public class Whip extends CondimentDecorator {
    
    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }
    
    public String getDesc() {
        return beverage.getDesc() + ", 휘핑크림";
    }
    
    public double cost() {
        return beverage.cost() + .10;
    }
}
```

### 3-4. 커피 주문 시스템 코드 테스트

```java
public static void main(String[] args) {
    Beverage beverage1 = new Espresso();
    System.out.println(beverage1.getDesc() + " $" + beverage1.cost());

    Beverage beverage2 = new DarkRoast();
    beverage2 = new Mocha(beverage2);
    beverage2 = new Mocha(beverage2);
    beverage2 = new Whip(beverage2);
    System.out.println(beverage2.getDesc() + " $" + beverage2.cost());

    Beverage beverage3 = new HouseBlend();
    beverage3 = new SoyMilk(beverage3);
    beverage3 = new Mocha(beverage3);
    beverage3 = new Whip(beverage3);
    System.out.println(beverage3.getDesc() + " $" + beverage3.cost());
}
```

### 주문 결과

<p align="center"><img src="decoratorPattern2.png" width="500"></p>

* 주문한 대로 가격 계산이 잘 된다.
* 코드의 유연성이 높아지긴 했으나 클래스가 정말 많이 생겼다.<br><br>

## 4. 데코레이터 패턴이 적용 된 예: 자바 I/O
* `java.io` 패키지는 데코레이터 패턴이 적용된 대표적인 예이다. 자바를 배우면서 `I/O` 패키지를 왜 이런식으로 만들어놨나 싶었는데 데코레이터 패턴을 배우고 나니까 그렇게 구성된 이유를 알 것 같다.
* 아직 완벽히 이해한 것은 아니지만 설계 이유를 알고 나니까 다음 부터는 필요한 부분만 잘 골라 쓸 수 있을 거 같다.
