---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 11 - 회원만 게시판 글 쓸 수 있게 하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Project Log
tags:
    - Project
    - Cafe
    - Log
---
# 개발환경
* MacBook Air (M1, 2020)
* OpenJDK 8
* Eclipse 2021-12
* tomcat 8.5
* MySQL Workbench 8.0.19<br><br><br>

# 기간
* 2022.3.4 ~ 2022.4.6<br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
* 로그인 한 회원만 게시판에 글을 쓸 수 있도록하는 유효성 검사를 추가했다.

## boardList.jsp

```jsp
<%
    String id = (String)session.getAttribute("id");
	
    boolean isLogin = false;
    if (null == id) isLogin = false;
    else isLogin = true;
%>
<section class="MOD_SUBNAVIGATION1">
    <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
        <h2>최신글 보기</h2>
        <p align="right"><button type="button" class="btn" id="writeBtn" 
            onclick="memberCheck(<%=isLogin%>);">글쓰기</button></p>
        <table class="type09">
        <colgroup>
            <col width="10%">
            <col width="60%">
            <col width="10%">
            <col width="10%">
            <col width="10%">
        </colgroup>
        <thead>
        <tr>
        <th scope="cols">No.</th>
        <th scope="cols">제목</th>
        <th scope="cols">작성자</th>
        <th scope="cols">작성일</th>
        <th scope="cols">조회수</th>
        </tr>
        </thead>
        <tbody>
        <tr>
        <th scope="row">번호</th>
        <td>내용이 들어갑니다.</td>
        <td>작성자</td>
        <td>작성일</td>
        <td>조회수</td>
        </tr>
        </tbody>
        </table>
    </div>
    </div>
</section>
```

## boardList.js

```javascript
function memberCheck(flag)
{
    if (flag)
    {
        location.href = './boardWrite.bo';
    }
    else 
    {
        alert('로그인 페이지로 이동합니다.');
        location.href = './login.me';
    }
}
```

* 현재 세션에 저장된 아이디 정보를 확인한 후 `자바스크립트`를 이용해 간단한 유효성 검사를 해 주었다.
* 이제 DB에 접속해서 작성한 글을 저장하는 기능을 만들려고 했는데 어제 맥을 업데이트 했더니 `MySQL Workbench`가 켜지지 않는다... 다운그레이드 하러 가야겠다.... 😔<br><br><br>

# 마감까지 
* `D-13`
