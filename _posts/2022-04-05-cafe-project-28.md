---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 28 - 관리자 페이지에서 게시글 관리 기능 추가
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

## 기간
* 2022.3.4 ~ 2022.4.6<br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황 1
* 관리자 페이지에서 전체 게시글 목록을 확인하고 여러 개를 선택해서 삭제할 수 있는 기능을 추가했다.

## boardManagement.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/adminLeftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
      <h3>게시글 관리 페이지</h3>
      <p style="font-size: medium;">글 제목을 클릭하면 내용을 볼 수 있습니다.</p><br>
      <%
      	String id = (String) session.getAttribute("id");
      	if (null == id)
            response.sendRedirect("./login.me");
      	else 
      	{
            if (!id.equals("admin"))
            {
                // 관리자 계정이 아니면 쫓아내기
                session.invalidate();
                response.sendRedirect("./login.me");
            }
      	}
      	
        int postCnt = (int)request.getAttribute("postCnt");
        ArrayList<BoardDTO> postList = (ArrayList<BoardDTO>)request.getAttribute("postList");
    	
        String pageNum = (String)request.getAttribute("pageNum");
        int pageCnt = (int)request.getAttribute("pageCnt");
        int pageBlockCnt = (int)request.getAttribute("pageBlockCnt");
        int startBlock = (int)request.getAttribute("startBlock");
        int endBlock = (int)request.getAttribute("endBlock");
      %>
      <form action="./AdminDeleteAction.bo" method="post" onsubmit="return finalCheck();">
        <table class="type09">
          <tbody>
            <colgroup>
              <col width="5%">
              <col width="10%">
              <col width="40%">
              <col width="15%">
              <col width="">
            </colgroup>
            <tr>
              <th><input type="checkbox" id="selectAll"></th>
              <th>글번호</th>
              <th>글제목</th>
              <th>글쓴이</th>
              <th>작성일</th>
            </tr>
            <%if (!boardList.isEmpty())
              {
                for (int i = 0; i < boardList.size(); i++)
                  { %>
            <tr>
              <td><input type="checkbox" name="post" value="<%=postList.get(i).getNum() %>"></td>
              <td><%=postList.get(i).getNum() %></td>
              <td><a href="./BoardContent.bo?num=<%=postList.get(i).getNum()%>&pageNum=1"><%=postList.get(i).getTitle() %></a></td>
              <td><%=postList.get(i).getId() %></td>
              <td><%=postList.get(i).getDate() %></td>
            </tr>
            <% }} %>
          </tbody>
        </table><br>
        <div id="boardPage">
          <%if (startBlock > pageBlockCnt) { %>
            <a href="./BoardList.bo?flag=a&pageNum=<%=startBlock - pageBlockCnt%>">[이전]</a>
          <%} %>
			
          <%for (int i = startBlock; i <= endBlock; i++) { %>
            <a href="./BoardList.bo?flag=a&pageNum=<%=i%>">[<%=i %>]</a>
          <%} %>
			
          <%if (endBlock > pageBlockCnt) { %>
            <a href="./BoardList.bo?flag=a&pageNum=<%=startBlock + pageBlockCnt%>">[다음]</a>
          <%} %>
		</div>
        <button type="submit" class="btn">게시글 삭제</button>
      </form>
    </div>
  </div>
</section>
```

* 전체 회원 목록을 보는 페이지의 레이아웃을 약간 수정해서 만들었다.
* 선택해서 삭제하는 기능이 동일하기 때문에 `javascript` 코드는 회원 목록 페이지의 코드를 그대로 썼다.
* 기존에 만들었던 자유게시판의 페이징 기능을 그대로 쓸 것이기 때문에 스크립틀릿의 코드도 게시글 목록을 보여주는 페이지의 코드를 그대로 썼다.

## adminLeftNav.jsp

```jsp
<div data-layout="al16 al-o2 de-o1 de6 ec4">
  <nav class="MOD_SUBNAVIGATION1_Menu" data-theme="_bo2">
    <p class="MOD_SUBNAVIGATION1_Menutitle" data-theme="_bgs">Menu</p>
    <ul>
      <li><a href="./MemberListAction.me">회원관리</a></li>
      <li><a href="./BoardList.bo?flag=a">게시글 관리</a></li>
    </ul>
  </nav>
</div>
```

* 게시글 관리 메뉴를 클릭하면 게시글 전체 목록을 불러오는 서블릿으로 이동한다.
* 기존에 만들었던 게시글 목록을 불러오는 서블릿을 재활용 할 것이기 때문에 파라미터로 함께 보내는 `flag`로 최종적으로 이동할 뷰 페이지를 결정한다.

## BoardManagementAction.java

```java
package com.project.cafe.board.action;

import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardListAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardListAction - execute() 호출");
		
        // 어디로 이동할 지 결정하는 정보 받아오기
        // a : 관리자용 게시글 관리 페이지
        // n : 일반 게시판
        String flag = request.getParameter("flag");
		
        // .. 중간 생략
		
        // 페이지 이동
        ActionForward forward = new ActionForward();
		
        if (flag.equals("a"))
        {
            forward.setPath("./admin/boardManagement.jsp");
            forward.setRedirect(false);
        }
        else 
        {
            forward.setPath("./contents/boardList.jsp");
            forward.setRedirect(false);			
        }
		
        return forward;
    }
}
```

* 게시글 목록을 불러오는 부분은 자유게시판에서 사용했던 것을 조금 수정해서 재활용했다.
* 매핑된 주소로 이동할 때 함께 받아온 `flag` 값으로 이동할 뷰 페이지를 구분한 뒤 자유게시판과 관리자용 게시글 관리 페이지로 이동한다.
* 생략한 코드는 [JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 12 - 게시판 글쓰기 기능 만들기](https://miro7923.github.io/project%20log/cafe-project-12/) 에서 확인할 수 있다.

* 게시글 목록을 모두 불러오고 나면 게시글 관리 페이지로 이동한다.

<p align="center"><img src="../../assets/images/boardManagementPage.png" width="600"></p>
<br><br>

# 진행상황 2
* 선택한 게시글을 일괄 삭제할 수 있는 기능을 추가했다.
* 삭제할 게시글들을 선택한 다음 게시글 관리 페이지 하단의 `게시글 삭제` 버튼을 누르면 일괄 삭제 동작을 수행할 서블릿으로 이동한다. 

## AdminDeleteAction.java

```java
package com.project.cafe.board.action;

import java.io.PrintWriter;
import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;

public class AdminDeleteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : AdminDeleteAction - execute() 호출");
		
        // 파라메터 받기
        String[] params = request.getParameterValues("post");
		
        ArrayList<Integer> postNums = new ArrayList<Integer>();
        for (String p : params)
            postNums.add(Integer.parseInt(p));
		
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
        out.print("<script>");
		
        // DB 접속해서 선택된 글들 삭제
        BoardDAO dao = new BoardDAO();
        for (int p : postNums)
        {
            int result = dao.deletePost(p);
            if (result != 1)
            {
                out.print("alert('잘못된 접근!');");
                out.print("history.back()");
                out.print("</script>");
				
                out.close();
				
                return null;
            }
        }
		
        // 게시글 관리 페이지로 이동
        out.print("alert('선택한 게시글 삭제가 완료 되었습니다.');");
        out.print("location.href='./BoardList.bo?flag=a';");
        out.print("</script>");
		
        out.close();
		
        return null;
    }
}
```

* 회원과 관련된 클래스들과 게시글과 관련된 클래스들을 각각 다른 패키지로 모아놓았기 때문에 클래스명은 회원 관리할 때 썼던 것과 같이 썼다.
* 게시글 관리 페이지로 돌아오면 업데이트 된 목록을 볼 수 있다.<br><br>

# 진행상황 3
* 게시글 하나의 내용을 확인하고 삭제할 수 있는 기능을 추가했다.

## boardManagement.jsp

```jsp
<%if (!boardList.isEmpty())
  {
    for (int i = 0; i < boardList.size(); i++)
    { %>
      <tr>
        <td><input type="checkbox" name="post" value="<%=boardList.get(i).getNum() %>"></td>
        <td><%=boardList.get(i).getNum() %></td>
        <td><a href="./BoardContent.bo?num=<%=boardList.get(i).getNum()%>&pageNum=1"><%=boardList.get(i).getTitle() %></a></td>
        <td><%=boardList.get(i).getId() %></td>
        <td><%=boardList.get(i).getDate() %></td>
      </tr>
<% }} %>
```

* 게시글 제목을 클릭하면 게시글 본문으로 이동하는 링크를 걸어주었다.

## boardContent.jsp

```jsp
<!-- 본인 글 일때만 수정/삭제 가능 -->
<!-- 관리자는 삭제만 가능 -->
<%if ((id != null && id.equals(coList.get(i).getId())) || (id != null && id.equals("admin"))) 
  {
    if (id.equals(coList.get(i).getId())) { %>
      <span>
        <a href="javascript:void(0);" onclick="showCommentBox(<%=i %>);" id="modify">수정&nbsp;</a>
      </span>
<% } %>
    <span>
      <a href="javascript:void(0);" onclick="confirmDelete(<%=coList.get(i).getNum()%>, <%=coList.get(i).getPost_num()%>, <%=pageNum%>);">삭제&nbsp;</a>
    </span>
<% } %>
```

* 관리자는 삭제만 할 수 있도록 조건 제한을 해 주었다.<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-2`
