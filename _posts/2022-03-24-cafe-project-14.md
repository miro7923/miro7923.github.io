---
title: 프로젝트) Cafe(웹 사이트) 만들기 14 - 게시글 수정 기능 구현
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
* 게시글 수정 기능을 만들었다.

## boardContent.jsp

```jsp
<%
    String id = (String)session.getAttribute("id");
	
    boolean isLogin = false;
    if (null == id) isLogin = false;
    else isLogin = true;
	
    BoardDTO dto = (BoardDTO)request.getAttribute("dto");
    String pageNum = (String)request.getAttribute("pageNum");
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
          <col width="45%">
          <col width="10%">
          <col width="20%">
          <col width="15%">
        </colgroup>
        <thead>
          <tr>
            <th scope="cols"><%=dto.getNum() %></th>
            <th scope="cols"><%=dto.getTitle() %></th>
            <th scope="cols"><%=dto.getId() %></th>
            <th scope="cols"><%=dto.getDate() %></th>
            <th scope="cols">조회수 <%=dto.getReadcount() %></th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td colspan="5" style="white-space:pre-wrap; word-wrap:break-word; word-break: break-all;"><%=dto.getContent() %><br><br><br></td>
          </tr>
          <tr>
            <td colspan="2">첨부파일</td>
            <td colspan="3"><%=dto.getFile() %></td>
          </tr>
        </tbody>
        </table><br>
        <div id="boardPage">
          <%if (null != id && id.equals(dto.getId())) { %>
            <button type="button" class="btn" 
              onclick="location.href='./BoardModify.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>';">수정하기</button>
            <button type="button" class="btn" 
              onclick="location.href='#';">삭제하기</button>
          <%} %>
          <button type="button" class="btn" 
            onclick="location.href='#';">답글쓰기</button>
          <button type="button" class="btn" 
            onclick="location.href='./BoardList.bo?pageNum=<%=pageNum%>';">목록이동</button>
      </div>
    </div>
  </div>
</section>
```

* 게시글을 보러 들어가면 본인이 쓴 글일 때에만 `수정하기` 버튼과 `삭제하기` 버튼이 활성화 된다.
* 게시글 본문은 엔터에 따라 줄바꿈이 되도록 본문을 출력하는 `td` 태그에만 줄바꿈용 스타일 적용을 해 주었다.

* `white-space` : 단어 사이 공백을 처리하는 속성. 
    * `pre-wrap` : 스페이스와 줄 바꿈을 보존하고 자동 줄 바꿈
* `word-wrap` : 띄어쓰기가 없는 긴 단어를 어떻게 처리할 지 정하는 속성. 
    * `break-word` : 요소의 경계에서 break point가 아니어도 줄바꿈
* `word-break` : 텍스트가 들어가는 블록 요소에서 단어 단위로 줄 바꿈을 할 지, 글자 단위로 줄 바꿈을 할 지 정하는 속성
    * `break-all` : 한글, 영문 모두 글자 단위로 줄 바꿈

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
        // ..생략
		
        // 2. 가상주소 매핑
        Action action = null;
        ActionForward forward = null;
		
        // .. 생략
        else if (command.equals("/BoardModify.bo"))
        {
            System.out.println("C : /BoardModify.bo 호출");
			
            action = new BoardModifyAction();
			
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
	
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        doProcess(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        doProcess(request, response);
    }
}
```

* 새로운 `Action class`인 `BoardModifyAction` 클래스를 만들어 이동한다.

## BoardModifyAction.java

```java
package com.project.cafe.board.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardModifyAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardModifyAction - execute() 호출");
		
        // 파라메터 저장
        int num = Integer.parseInt(request.getParameter("num"));
        String pageNum = request.getParameter("pageNum");
		
        // DB에서 글 정보 가져오기
        BoardDAO dao = new BoardDAO();
        BoardDTO dto = dao.getPost(num);
		
        // request 영역에 글 정보 저장
        request.setAttribute("dto", dto);
        request.setAttribute("pageNum", pageNum);
		
        // 페이지 이동정보 설정
        ActionForward forward = new ActionForward();
        forward.setPath("./contents/boardModify.jsp");
        forward.setRedirect(false);
		
        return forward;
    }
}
```

* DB에 가서 게시글 수정 페이지에 출력할 기존 글 정보를 가져온 뒤 수정 페이지로 이동한다.

## boardModify.jsp

```jsp
<%
    String id = (String)session.getAttribute("id");
    if (null == id)
        response.sendRedirect("./login.me");
	
    BoardDTO dto = (BoardDTO)request.getAttribute("dto");
    String pageNum = (String)request.getAttribute("pageNum");
%>
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
    	<h2>게시글 수정</h2>
    	<form name="write" action="./BoardModifyPro.bo" method="post" onsubmit="return finalCheck();">
    	<input type="hidden" name="id" value="<%=id%>">
        <input type="hidden" name="num" value="<%=dto.getNum()%>">
        <input type="hidden" name="pageNum" value="<%=pageNum%>">
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">제목 </label>
          <input type="text" name="title" id="title" value="<%=dto.getTitle()%>">
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_MsgField">내용 </label>
          <textarea id="MOD_TEXTFORM_MsgField" name="content"><%=dto.getContent() %></textarea>
        </div>

        <button type="submit" class="btn">글 수정하기</button>
      </form>
      </div>
  </div>
</section>
```

* DB에서 가져온 기존 글 정보를 출력한다.

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
        else if (command.equals("/BoardModifyPro.bo"))
        {
            System.out.println("C : /BoardModifyPro.bo 호출");
			
            action = new BoardModifyProAction();
			
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

* 수정한 내용을 DB에 저장하는 동작을 수행할 서블릿으로 이동한다.

## BoardModifyProAction.java

```java
package com.project.cafe.board.action;

import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardModifyProAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardModifyProAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("UTF-8");
		
        // 파라메터 저장
        int num = Integer.parseInt(request.getParameter("num"));
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        String pageNum = request.getParameter("pageNum");
		
        // DB에서 기존 글 정보를 찾은 다음 새 내용으로 수정
        BoardDTO dto = new BoardDTO();
        dto.setNum(num);
        dto.setTitle(title);
        dto.setContent(content);

        BoardDAO dao = new BoardDAO();
        int result = dao.modifyPost(dto);
		
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        if (1 != result)
        {
            // 해당 글 정보 없음
            out.println("<script>");
            out.println("alert('해당 글 정보 없음!');");
            out.println("location.href='./BoardList.bo';");
            out.println("</script>");
			
            return null;
        }
		
        // 게시글 내용 페이지로 이동
        out.println("<script>");
        out.println("location.href='./BoardContent.bo?num="+num+"&pageNum="+pageNum+"';");
        out.println("</script>");
		
        out.close();
		
        return null;
    }
}
```

* DB에 접속해서 `update` 동작을 수행한 뒤 수정 내용을 확인할 수 있도록 게시글 본문 페이지로 이동한다.
* 게시글 본문 페이지로 이동할 때 본문 내용을 불러오는 서블릿이 호출되기 때문에 글 번호와 페이지정보만 파라미터로 넘겨주면 된다.
* 로그인 한 사용자 중 본인 글일 때에만 수정할 수 있게 해 놔서 예외 상황은 안 생길 것이라고 보지만 혹시 모르니까 예외처리를 해 놓았다.

## BoardDAO.java - modifyPost(dto)

```java
public int modifyPost(BoardDTO dto)
{
    int ret = -1;
		
    try {
        con = getCon();
			
        // 번호로 해당 글 찾기
        sql = "select * from cafe_board where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, dto.getNum());
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            // update 동작 수행
            sql = "update cafe_board set title=?, content=? where num=?";
            pstmt = con.prepareStatement(sql);
				
            pstmt.setString(1, dto.getTitle());
            pstmt.setString(2, dto.getContent());
            pstmt.setInt(3, dto.getNum());
				
            ret = pstmt.executeUpdate();
				
            System.out.println("DAO : 글 수정 완료");
        }
        else ret = -1;
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
		
    return ret;
}
```

* 이제 게시글 본문 페이지로 이동하면 수정한 내용이 적용되어 보이고 `DB`에도 수정된 내용이 반영되어 있다.

* 다음엔 게시글 삭제 기능을 만들 것이다.<br><br><br>

# 마감까지 
* `D-11`
