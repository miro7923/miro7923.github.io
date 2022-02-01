---
title: JSP 액션 태그
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
# 👀 액션 태그란?

```jsp
<jsp:~ 형태로 시작하는 태그>
```

* `JSP` 페이지에서 `HTML` 태그 형태로 `JSP` 코드의 역할을 수행하는 것
* 스크립틀릿 등의 스크립트 요소(자바 코드)를 사용하지 않기 때문에 `JSP` 페이지의 내부적인 프로그램 로직을 사용자로부터 감출 수 있다.
* 즉, 사용자에게 보여지는 프레젠테이션 부분과 사용자의 요청을 처리하는 비즈니스 로직 부분(프로그램 부분)을 분리하는 것이 가능하다.
* 프로그램 재사용성을 높여주고 코드의 간결성을 향상시킨다.

## 🔸 < jsp:forward >

```jsp
<jsp:forward page="이동할 페이지(ex. forward.jsp)" />
<jsp:forward page="이동할 페이지"></jsp:forward>
```

* 액션 태그는 `XML` 문법을 이용하여 구현된 기능이므로 태그의 종료 끝에 반드시 종료 태그가 있어야 한다.
* `forward` 액션은 현재 페이지의 요청과 응답에 관한 처리권을 `page` 속성에 지정된 이동할 페이지로 영구적으로 넘기는 기능을 한다.

## 🔸 < jsp:include >

```jsp
<jsp:include page="이동할 페이지(ex. forward.jsp)" flush="false" />
<jsp:include page="이동할 페이지(ex. forward.jsp)" flush="false"></jsp:include>
```

* 특정 페이지를 `include` 한다.
* 각각의 페이지를 컴파일 후 해당 파일을 `include` 한다.
* top, bottom과 같은 동일 사용 페이지를 추가할 때 사용한다.

## 🔸 < jsp:param >

```jsp
<jsp:param name="파라미터 이름1" value="파라미터 값1" />
<jsp:param name="파라미터 이름2" value="파라미터 값2" />
```

* `forward`와 `include` 태그를 사용하여 이동할 페이지에 추가적으로 넘겨줄 파라미터가 있으면 `<jsp:param/>` 태그를 사용할 수 있다.
