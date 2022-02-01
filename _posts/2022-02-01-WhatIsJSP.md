---
title: JSP란?
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - WEB
---
# 👀 JSP(Java Server Page)란?
* `Java`를 이용하여 `동적인 웹 페이지`를 만들기 위해 Sun Microsystems사가 개발한 기술<br><br>

# JSP의 특징
## 1. 강력한 이식성
* `자바기반`의 언어로 어떤 `JSP 컨테이너`에서도 사용이 가능하므로 한 번 작성한 코드를 별다른 수정 없이 다른 플랫폼으로 이식이 가능하다.
* `모듈화`와 `모듈의 재사용성`이 좋다.

## 2. 서버 자원의 효율적인 사용
* `스레드(Thread)` 기반의 아키텍처 사용으로 불필요한 자원 낭비를 감소시켰다.

## 3. 간편한 MVC 패턴(디자인 패턴)
* `MVC 패턴`을 `JSP(View)`와 `자바빈즈(Model)`, `서블릿(Controller)`을 이용해 쉽게 구현할 수 있다.

### 🔸 MVC 패턴
* 사용자에게 보여지는 화면인 `View` 부분과 실제 비즈니스 로직이 들어가는 `Model` 부분 그리고 `View`와 `Model`을 연결시켜주는 `Controller` 부분으로 구성
* 최근에 중대형 프로젝트에서 효과적이라 평가되어 많이 사용되고 있다.

### 🔸 디자인 패턴
* 프로젝트를 개발함에 있어서 특정한 문제가 주어졌을 때 그 문제를 해결하기 위한 방법을 설명해 놓은 일종의 지침

