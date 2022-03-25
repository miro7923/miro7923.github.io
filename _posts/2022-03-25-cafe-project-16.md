---
title: 프로젝트) Cafe(웹 사이트) 만들기 16 - 답글 작성 기능 구현
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

# 시작
* 2022.3.4 ~ <br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
* 게시글에 답글을 작성할 수 있는 기능을 만들었다.

## boardContent.jsp

```jsp
<div id="boardPage">
  <%if (null != id && id.equals(dto.getId())) { %>
    <button type="button" class="btn" 
      onclick="location.href='./BoardModify.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>';">수정하기</button>
    <button type="button" class="btn" 
      onclick="location.href='./BoardDeleteConfirm.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>';">삭제하기</button>
  <%} %>
    <button type="button" class="btn" 
      onclick="location.href='./BoardReWrite.bo?num=<%=dto.getNum()%>&re_ref=<%=dto.getRe_ref()%>&re_lev=<%=dto.getRe_lev()%>&re_seq=<%=dto.getRe_seq()%>';">답글쓰기</button>
    <button type="button" class="btn" 
      onclick="location.href='./BoardList.bo?pageNum=<%=pageNum%>';">목록이동</button>
</div>
```

* 답글을 작성할 수 있는 페이지로 연결하는 링크를 추가했다.
* 답글을 작성하려는 게시글 아래에 새로 작성한 답글을 배치할 것이기 때문에 작성하려는 답글 그룹의 정보인 `re_ref`, 답글의 들여쓰기 레벨 정보인 `re_lev`, 답글이 작성된 순서 정보인 `re_seq`들을 파라메터로 넘긴다.

## BoardFrontController.java

```java
package com.project.cafe.board.action;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class BoardFrontController extends HttpServlet
{
    protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        // 1. 전달되는 가상주소 계산
        // .. 생략
		
        // 2. 가상주소 매핑
        Action action = null;
        ActionForward forward = null;
		
        // .. 생략
        else 
        {
            forward = new ActionForward();

            // .. 생략
            else if (command.equals("/BoardReWrite.bo"))
            {
                System.out.println("C : /BoardReWrite.bo 호출");
				
                forward.setPath("./contents/boardReWrite.jsp");
            }

            forward.setRedirect(false);
        }
		
        // 3. 페이지 이동
        // .. 생략
    }
}
```

* 컨트롤러에서 답글을 작성하는 페이지로 이동한다.

## boardReWrite.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">

<!-- Start Head -->
  <jsp:include page="../inc/top.jsp"></jsp:include>
  <script src="${pageContext.request.contextPath }/js/boardWrite.js"></script>
<!-- End Head -->

<body class="modern">

<!--
START MODULE AREA 2: Menu 1
-->
  <jsp:include page="../inc/subTop.jsp"></jsp:include>
<!--
END MODULE AREA 2: Menu 1
-->

<!--
START MODULE AREA 3: Sub Navigation 1
-->
<%
    String id = (String)session.getAttribute("id");
    if (null == id)
        response.sendRedirect("./login.me");
%>
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
      <h2>답글 작성</h2>
      <form name="write" action="./BoardReWriteAction.bo" method="post" onsubmit="return finalCheck();">
    	
        <input type="hidden" name="id" value="<%=id%>">
        <input type="hidden" name="num" value="<%=request.getParameter("num")%>">
        <input type="hidden" name="re_ref" value="<%=request.getParameter("re_ref")%>">
        <input type="hidden" name="re_lev" value="<%=request.getParameter("re_lev")%>">
        <input type="hidden" name="re_seq" value="<%=request.getParameter("re_seq")%>">
    	
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">제목 </label><input type="text" name="title" id="title">
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_MsgField">내용 </label>
          <textarea id="MOD_TEXTFORM_MsgField" name="content"></textarea>
        </div>

        <button type="submit" class="btn">글 등록</button>
      </form>
    </div>
  </div>
</section>
<!--
END MODULE AREA 3: Sub Navigation 1
-->

<!--
START MODULE AREA 5: Footer 2
-->
  <jsp:include page="../inc/bottom.jsp"></jsp:include>
<!--
END MODULE AREA 5: Footer 2
-->

<script src="${pageContext.request.contextPath }/js/index.js"></script>
</body>

</html>
```

* 게시글을 작성하는 페이지인 `boardWrite.jsp`와 크게 달라지는 것이 없어서 복사해서 썼다.
* 답글 작성에 필요한 정보들만 추가로 저장하는 `input` 태그를 추가했다.

## BoardFrontController.java

```java
package com.project.cafe.board.action;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class BoardFrontController extends HttpServlet
{
    protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        // 1. 전달되는 가상주소 계산
        // .. 생략
		
        // 2. 가상주소 매핑
        Action action = null;
        ActionForward forward = null;
		
        // .. 생략
        else if (command.equals("/BoardReWriteAction.bo"))
        {
            System.out.println("C : /BoardReWriteAction.bo 호출");
			
            action = new BoardReWriteAction();
			
            try {
                forward = action.execute(request, response);
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
		
        // 3. 페이지 이동
        // .. 생략
    }
}
```

* `DB`에 접속해서 새 글을 삽입하는 동작을 수행해야 하니까 서블릿으로 연결한다.

## BoardReWriteAction.java

```java
package com.project.cafe.board.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardReWriteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardReWriteAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("UTF-8");
		
        // 파라메터 저장
        BoardDTO dto = new BoardDTO();
        dto.setContent(request.getParameter("content"));
        dto.setId(request.getParameter("id"));
        dto.setIp(request.getRemoteAddr());
        dto.setNum(Integer.parseInt(request.getParameter("num")));
        dto.setRe_lev(Integer.parseInt(request.getParameter("re_lev")));
        dto.setRe_ref(Integer.parseInt(request.getParameter("re_ref")));
        dto.setRe_seq(Integer.parseInt(request.getParameter("re_seq")));
        dto.setReadcount(0);
        dto.setTitle(request.getParameter("title"));
		
        // DB 연결해서 글 저장
        BoardDAO dao = new BoardDAO();
        dao.reWritePost(dto);
		
        // 게시글 목록으로 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./BoardList.bo");
        forward.setRedirect(true);
		
        return forward;
    }
}
```

* 새 글을 삽입하는 동작이기 때문에 기본적인 틀은 `BoardWriteAction.java`와 같지만 부모가 되는 게시글을 설정해 주어야 하기 때문에 `DAO` 객체의 호출 함수가 다르다.

## BoardDAO.java - reWritePost(dto)

```java
public void reWritePost(BoardDTO dto)
{
    int curNum = 0; // 이번에 쓸 글 번호
		
    try {
        con = getCon();
			
        // 글 번호 계산
        sql = "select max(num) from cafe_board";
        pstmt = con.prepareStatement(sql);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
            curNum = rs.getInt(1) + 1;
			
        System.out.println("DAO : 답글번호 : "+curNum);
			
        // 답글순서 재배치(seq 변경)
        // re_ref 가 같은 그룹내에서 update 동작 수행 - re_seq값이 기존값보다 큰 값이 있을 때 re_seq + 1
        // 없으면(0) 정렬할 게 없으니까 그냥 지나갈 것임
        sql = "update cafe_board set re_seq = re_seq + 1 where re_ref = ? and re_seq > ?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, dto.getRe_ref());
        pstmt.setInt(2, dto.getRe_seq());
			
        int result = pstmt.executeUpdate();
        if (0 < result)
            System.out.println("DAO : 답글 순서 재배치");
			
        // 답글 저장 동작 수행
        sql = "insert into cafe_board(num, id, title, content, readcount, re_ref, re_lev, re_seq, date, ip, file)"
            + " values(?,?,?,?,?,?,?,?,now(),?,?)";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, curNum);
        pstmt.setString(2, dto.getId());
        pstmt.setString(3, dto.getTitle());
        pstmt.setString(4, dto.getContent());
        pstmt.setInt(5, 0);
			
        pstmt.setInt(6, dto.getRe_ref()); // 게시글 그룹
        pstmt.setInt(7, dto.getRe_lev() + 1); // 들여쓰기 - 기준글 + 1
        pstmt.setInt(8, dto.getRe_seq() + 1); // 그룹 내 순서 - 기준글 + 1
			
        pstmt.setString(9, dto.getIp());
        pstmt.setString(10, dto.getFile());
			
        pstmt.executeUpdate();
			
        System.out.println("DAO : 답글 작성 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
}
```

* 답글을 작성할 때 `re_ref` 필드를 이용해 부모 글을 설정해 준 다음 들여쓰기 레벨과 게시글 그룹 내 순서를 설정해 준다.
* 자세한 알고리즘은 [여기](#) 포스트에 따로 작성했다.

## boardList.jsp

```jsp
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
<%
    if (null != postList) {
        for (int i = 0; i < postList.size(); i++) 
        {
            BoardDTO dto = postList.get(i);
%>
  <tr>
    <th scope="row"><%=dto.getNum() %></th>
    <td>
    <%
        int width = 0;
        if (0 < dto.getRe_lev()) // 답글일 때
        {
            width = 10 * dto.getRe_lev();
    %>
      <img class="reImg" src="./contents/level.gif" width="<%=width%>" height="15">
      <img class="reImg" src="./contents/re.gif">
    <%
        }
    %>
      <a href="./BoardContent.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>"><%=dto.getTitle() %></a>
    </td>
    <td><%=dto.getId() %></td>
    <td><%=dto.getDate() %></td>
    <td><%=dto.getReadcount() %></td>
  </tr>
<%}} %>
</tbody>
```

* 새 글 삽입 과정이 끝나고 나면 게시글 목록에서 새로 작성된 답글을 확인할 수 있다.
* 답글은 따로 표시하기 위해서 이미지를 추가했다.
* 이 때 더미 이미지(`level.gif`)를 이용해 들여쓰기 레벨에 따라 들여쓰기를 먼저 한 뒤 답글 이미지(`re.gif`)를 표시하고 제목을 출력한다.

<p align="center"><img src="../../assets/images/replyList.png" width="600"></p>

* 다음에 만들 기능은 파일 업로드 기능!<br><br><br>

# 마감까지 
* `D-10`
