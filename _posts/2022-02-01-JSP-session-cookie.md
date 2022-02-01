---
title: 세션(Session)과 쿠키(Cookie)
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - WEB
    - Session
    - Cookie
---
# 👀 세션(Session)이란?
## ☑️ 서버랑 클라이언트와의 관계(상태)를 유지하기 위해서 사용하는 값
* `HTTP 프로토콜`은 상태가 유지되지 않기 때문에 요청에 대한 응답이 한 번씩 이뤄지고 나면 그 관계가 소멸된다. 
* 하지만 이런 방식으로는 사용자가 회원 사이트에 로그인 했을 때 지속적으로 업무를 볼 수 없게 된다.
* 그래서 사용자가 지속적으로 업무를 볼 수 있도록 서버와 클라이언트간의 관계를 일정 시간동안 유지해주기 위해 쓰는 것이 `세션`이다.
* 세션객체(영역)는 브라우저당 1개씩 생성된다.

## 🔸 세션 메소드
* 세션값 생성 : `session.setAttribute("이름", 값);`
* 세션값 사용 : `session.getAttribute("이름");`
* 세션값 제거 : `session.removeAttribute("이름");`
* 세션값 초기화 : `session.invalidate();`
    * ❗️ 세션값 제거 메소드를 통한 부분제거보다는 세션값 초기화를 시켜야 한다.
    * 일부만 제거하면 사용자가 로그아웃 했는데 로그인 했을 때와 같은 행동이 일부 가능할 수 있다.
<br><br><br>

# 👀 쿠키(Cookie)란?
* `클라이언트` 측에서 관리되는 정보
* 사용 가능한 기간동안 하드디스크에 저장되기 때문에 웹 브라우저가 사라져도 사용이 가능하다.
* 하지만 그만큼 보안적으로 취약하다. (그래서 보안이 필요한 정보는 `세션` 사용)

## 🔸 쿠키 생성 절차
### 1. 쿠키 생성
### 2. 쿠키가 필요로 하는 설정값 저장(유효기간, 설명, 도메인, ...)
### 3. 웹 브라우저에 생성된 쿠키 정보 전달
### 4. 웹 브라우저 요청에서 쿠키를 얻어온다.
### 5. 쿠키 정보는 이름, 값의 데이터 쌍으로 형성된다.
### 6. 쿠키 이름을 사용해서 해당 값을 사용한다.

## 🔸 쿠키 사용 방법
### 1) `HTTP` 헤더 정보 사용 - 안씀 XX
### 2) 서블릿 API 사용 - 현재 쓰는 방식

* 쿠키 생성하는 예제 코드

```jsp
<%
    // 쿠키값 생성 - 서블릿 API 사용
    Cookie cookie = new Cookie("name", "CookieValue"); // HDD 저장X, 메모리에만 있는 상태
		
    // 쿠키값 설정 - 유효기간 설정
    cookie.setMaxAge(600); // 초 단위 - 10분
		
    // 쿠키값을 저장(응답정보(response)에 저장)
    response.addCookie(cookie);
    System.out.println("쿠키정보가 전달 완료시 사용자의 HDD에 저장");
%>
``` 

* 먼저 쿠키를 생성한 다음 클라이언트에게 전송한다.

* 쿠키값 가져오는 예제 코드

```jsp
<%
    // 쿠키값 가져오기(request - 요청정보로부터 꺼내오기)
    Cookie[] cookies = request.getCookies();
			
    if (null != cookies) // !!배열을 반복문 돌리기 전에 예외처리 꼭 하기!!
    {
        for (int i = 0; cookies.length > i; i++)
        {
            //System.out.println(cookies[i]);
            if (cookies[i].getName().equals("name"))
            {
                // Cookie cookie = new Cookie("name", "CookieValue"); // HDD 저장X, 메모리O
                // ⬆️ 얘를 가져올 것임
                // 이름이 같다면 내가 직접 생성한 쿠키임
                System.out.println(cookies[i].getValue());
						
                out.print("쿠키명: " + cookies[i].getName() + "\n");
                out.print("쿠키값 : " + cookies[i].getValue());
            }
        }
    }
%>
```

* 그 다음 클라이언트 측에서 쿠키를 가져온다.
* 쿠키값을 가져올 때엔 꼭 예외처리를 해서 `null`값이 아닐 때에만 가져오는 동작을 수행하도록 해야한다.
