---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 13 - 게시글 조회 기능 만들기
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
* 게시판 목록에서 게시글을 선택하면 내용을 볼 수 있는 기능을 만들었다.

## boardList.jsp

```jsp
...생략

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
      <a href="./BoardContent.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>"><%=dto.getTitle() %></a>
    </td>
    <td><%=dto.getId() %></td>
    <td><%=dto.getDate() %></td>
    <td><%=dto.getReadcount() %></td>
  </tr>
<%}} %>
</tbody>

...생략
```

* 글 목록을 출력하는 부분에서 `a` 태그를 사용해 `하이퍼링크`를 추가했다.
* 글 번호와 현재 페이지 정보를 `get` 방식으로 파라미터로 넘겨준다.
* 페이지 정보를 넘겨주는 이유는 글을 보고 나서 목록으로 다시 돌아갈 때 사용자가 이전에 있던 페이지로 이동하기 위해서다.

## BoardFrontController.java

```java
// .. 생략

protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
{
    // .. 생략
    
    // 2. 가상주소 매핑
    Action action = null;
    ActionForward forward = null;
    
    // .. 생략

    else if (command.equals("/BoardContent.bo"))
    {
        System.out.println("C : /BoardContent.bo 호출");
			
        action = new BoardContentAction();
			
        try {
            forward = action.execute(request, response);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
    
// .. 생략
```

* 글 제목을 누르면 `Controller`로 와서 글 내용을 보여주는 페이지로 연결된다.

## BoardContentAction.java

```java
package com.project.cafe.board.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardContentAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardContentAction - execute() 호출");
		
        // 전달받은 파라메터(글 번호, 페이지정보) 저장
        int num = Integer.parseInt(request.getParameter("num"));
        String pageNum = request.getParameter("pageNum"); // 페이지번호는 DB 처리할 때 사용하진 않아서 형변환 필요 X
		
        // DAO 객체 생성
        BoardDAO dao = new BoardDAO();

        // 글 조회수 1 증가
        dao.updateReadCount(num);
		
        // 글 정보 불러오기
        BoardDTO dto = dao.getPost(num);
		
        // request 영역에 글 정보랑 페이지정보 저장
        request.setAttribute("dto", dto);
        request.setAttribute("pageNum", pageNum);
		
        // 페이지 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./contents/boardContent.jsp");
        forward.setRedirect(false);
		
        return forward;
    }
}
```

* `Action class`에 오면 `DB`와 연결해서 글 조회수를 증가시킨 후 글 정보를 가져오는 동작을 수행한다.

## BoardDAO.java - updateReadCount(num)

```java
public void updateReadCount(int num)
{
    try {
        con = getCon();
			
        sql = "update cafe_board set readcount = readcount + 1 where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, num);
			
        pstmt.executeUpdate();
			
        System.out.println("DAO : 조회수 1 증가 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
}
```

* 글 정보를 가져오기에 앞서 조회수를 먼저 증가시킨다.

## BoardDAO.java - getPost(num)

```java
public BoardDTO getPost(int num)
{
    BoardDTO dto = null;
		
    try {
        con = getCon();
			
        sql = "select * from cafe_board where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, num);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            dto = new BoardDTO();
				
            dto.setContent(rs.getString("content"));
            dto.setDate(rs.getDate("date"));
            dto.setFile(rs.getString("file"));
            dto.setId(rs.getString("id"));
            dto.setIp(rs.getString("ip"));
            dto.setNum(rs.getInt("num"));
            dto.setRe_lev(rs.getInt("re_lev"));
            dto.setRe_ref(rs.getInt("re_ref"));
            dto.setRe_seq(rs.getInt("re_seq"));
            dto.setReadcount(rs.getInt("readcount"));
            dto.setTitle(rs.getString("title"));
				
            System.out.println("DAO : 글 1개 정보 저장 완료");
        }
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
		
    return dto;
}
```

* 조회수 증가가 완료되면 글 1개의 정보를 가져온다.

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
            <td colspan="5"><%=dto.getContent() %></td>
          </tr>
          <tr>
            <td colspan="2">첨부파일</td>
            <td colspan="3"><%=dto.getFile() %></td>
          </tr>
        </tbody>
      </table><br>
      <div id="boardPage">
      <button type="button" class="btn" 
        onclick="location.href='#';">수정하기</button>
      <button type="button" class="btn" 
        onclick="location.href='#';">삭제하기</button>
      <button type="button" class="btn" 
        onclick="location.href='#';">답글쓰기</button>
      <button type="button" class="btn" 
        onclick="location.href='./BoardList.bo?pageNum=<%=pageNum%>';">목록이동</button>
      </div>
    </div>
  </div>
</section>
```

* 이제 글 내용을 보여주는 페이지로 오면 `DB`에서 불러온 내용을 화면에 출력한다.
* 하단에 `목록이동` 버튼을 누르면 글을 보기 전에 머물러 있던 페이지로 이동한다.

<p align="center"><img src="../../assets/images/boardContent.png"></p>

* 연결 성공!
* 다음에는 게시글 수정 기능을 만들 것이다.<br><br><br>

# 마감까지 
* `D-11`
