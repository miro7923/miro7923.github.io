---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 23 - 댓글 달기 기능 추가
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

# 진행상황 1
* 게시글에 달린 댓글을 볼 수 있는 기능을 추가했다.

## boardList.jsp

```jsp
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
      <a href="./BoardContent.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>"><%=dto.getTitle() %> (<%=dto.getComment_count() %>)</a>
    </td>
    <td><%=dto.getId() %></td>
    <td><%=dto.getDate() %></td>
    <td><%=dto.getReadcount() %></td>
  </tr>
<%}} %>
</tbody>
```

* 게시글 목록에서 댓글이 달린 갯수를 확인할 수 있게 수정했다.

## BoardDAO.java - getPostList(startRow, pageSize)

```java
public ArrayList<BoardDTO> getPostList(int startRow, int pageSize)
{
    ArrayList<BoardDTO> postList = new ArrayList<BoardDTO>();
		
    try {
        con = getCon();
			
        // 글 자르기 : limit 시작행-1, 갯수
        // 시작행-1부터 x개 만큼 가져온다.
        // 정렬 : re_ref(내림차순) / re_seq(오름차순)
        sql = "select * from cafe_board order by re_ref desc, re_seq asc limit ?,?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, startRow - 1); // 시작행 - 1
        pstmt.setInt(2, pageSize); // 갯수
			
        rs = pstmt.executeQuery();
			
        while (rs.next())
        {
            // 글 1개의 정보(dto)에 저장한 후 배열에 저장
            BoardDTO dto = new BoardDTO();
				
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
            dto.setComment_count(rs.getInt("comment_count"));
				
            postList.add(dto);
        }
			
        System.out.println("DAO : 글 정보 저장 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
		
    return postList;
}
```

* 게시글 목록을 불러올 때 댓글의 갯수도 함께 저장하도록 수정했다.
* 물론 테이블에도 댓글 갯수 컬럼을 추가했다.

<p align="center"><img src="../../assets/images/boardListComment.png"></p>

## boardContent.jsp

```jsp
<%
    String id = (String)session.getAttribute("id");
	
    boolean isLogin = false;
    if (null == id) isLogin = false;
    else isLogin = true;
	
    BoardDTO dto = (BoardDTO)request.getAttribute("dto");
    String pageNum = (String)request.getAttribute("pageNum");
    ArrayList<CommentDTO> coList = (ArrayList<CommentDTO>)request.getAttribute("coList");
%>

<!-- 게시글 본문 영역 -->

<!-- 댓글 영역 -->  
<div class="comments">
  <h3>댓글</h3>
  <input type="hidden" id="postNum" value="<%=dto.getNum()%>">
  <p>
    <%if (!coList.isEmpty()) {
        for (int i = 0; i < coList.size(); i++) { 
            CommentDTO coDto = coList.get(i); %>
    <span class="comId">
      <%=coDto.getId() %>&nbsp;		
    </span>
    <span class="comDate">
      <%=coDto.getCommentedDate() %>&nbsp;
    </span>
    <span>
      <a href="#">수정&nbsp;</a>
    </span>
    <span>
      <a href="#">삭제&nbsp;</a>
    </span>
  </p>
  <p class="comContent">
    <%=coDto.getContent() %>
  </p><br>
  <% }} %>
</div>
<!-- 댓글 영역 -->

<!-- 하단 버튼 영역 -->
```

* 게시글 본문 페이지에 작성된 댓글들을 볼 수 있는 공간을 추가했다.
* 게시글 본문을 불러오는 서블릿에서 댓글 목록도 함께 불러와서 출력한다.

## BoardContentAction.java

```java
package com.project.cafe.board.action;

import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;
import com.project.cafe.board.db.CommentDTO;

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
        
        // 해당 글에 달린 댓글 정보 불러오기
        ArrayList<CommentDTO> coList = dao.getComments(num);
		
        // request 영역에 글 정보랑 페이지정보, 댓글목록 저장
        request.setAttribute("dto", dto);
        request.setAttribute("pageNum", pageNum);
        request.setAttribute("coList", coList);
		
        // 페이지 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./contents/boardContent.jsp");
        forward.setRedirect(false);
		
        return forward;
    }
}
```

* 게시글 내용을 불러올 때 해당 글에 달린 댓글 정보도 함께 불러온다.

## BoardDAO.java - getComments(num)

```java
public ArrayList<CommentDTO> getComments(int num)
{
    ArrayList<CommentDTO> list = new ArrayList<CommentDTO>();
		
    try {
        con = getCon();
			
        sql = "select * from cafe_comment where post_num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, num);
			
        rs = pstmt.executeQuery();
			
        while (rs.next())
        {
            CommentDTO dto = new CommentDTO();
				
            dto.setCommentedDate(rs.getDate("commented_date"));
            dto.setContent(rs.getString("content"));
            dto.setId(rs.getString("id"));
            dto.setIp(rs.getString("ip"));
            dto.setNum(rs.getInt("num"));
            dto.setPost_num(rs.getInt("post_num"));
            dto.setRe_lev(rs.getInt("re_lev"));
            dto.setRe_ref(rs.getInt("re_ref"));
            dto.setRe_seq(rs.getInt("re_seq"));
				
            list.add(dto);
        }
			
        System.out.println("DAO : 댓글 목록 저장 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
		
    return list;
}
```

* 게시글 번호를 매개변수로 받아서 해당 글에 달린 댓글들만 불러온다.<br><br>

<p align="center"><img src="../../assets/images/commentEmpty.png"></p>

* 작성된 댓글이 없을 때엔 불러오지 않는다.<br><br>

# 진행상황 2
* 게시글에 댓글을 다는 기능을 추가했다.

## boardContent.jsp

```jsp
<!-- 게시글 본문 영역 -->

<!-- 댓글 영역 -->  
<form action="./CommentWriteAction.bo?post_num=<%=dto.getNum() %>&pageNum=<%=pageNum %>" method="post" onsubmit="return writeComment();">
  <input type="hidden" name="id" value="<%=id%>">
  <input type="text" id="comment" name="comment" placeholder="댓글 달기". maxlength="500">
  <button type="submit" class="btn" id="commentBtn">입력</button>
</form><br><br>
<div class="comments">
  <h3>댓글</h3>
  <input type="hidden" id="postNum" value="<%=dto.getNum()%>">
  <p>
    <%if (!coList.isEmpty()) {
        for (int i = 0; i < coList.size(); i++) { 
            CommentDTO coDto = coList.get(i); %>
    <span class="comId">
      <%=coDto.getId() %>&nbsp;		
    </span>
    <span class="comDate">
      <%=coDto.getCommentedDate() %>&nbsp;
    </span>
    <span>
      <a href="#">수정&nbsp;</a>
    </span>
    <span>
      <a href="#">삭제&nbsp;</a>
    </span>
  </p>
  <p class="comContent">
    <%=coDto.getContent() %>
  </p><br>
  <% }} %>
</div>
<!-- 댓글 영역 -->

<!-- 하단 버튼 영역 -->
```

* 작성한 댓글을 전송할 폼태그를 추가했다.

## boardContent.js

```javascript
function writeComment()
{
    if ($('#comment').val().length <= 0)
    {
        alert('내용을 입력하세요.');
        return false;
    }
    else
        return true;
}
```

* 댓글 작성 서블릿으로 이동하기 전에 댓글이 작성된 것을 확인한 후 서블릿으로 이동한다.

## CommentWriteAction.java

```java
package com.project.cafe.board.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.CommentDTO;

public class CommentWriteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : CommentWriteAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("utf-8");
		
        // 파라메터 저장
        String id = request.getParameter("id");
        String comment = request.getParameter("comment");
        int postNum = Integer.parseInt(request.getParameter("post_num"));
        String pageNum = request.getParameter("pageNum");
		
        CommentDTO dto = new CommentDTO();
        dto.setContent(comment);
        dto.setId(id);
        dto.setIp(request.getRemoteAddr());
        dto.setPost_num(postNum);
		
        // DB 연결해서 댓글 저장
        BoardDAO dao = new BoardDAO();
        dao.insertComment(dto);
		
        // 페이지 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./BoardContent.bo?num="+postNum+"&pageNum="+pageNum);
        forward.setRedirect(true);
		
        return forward;
    }
}
```

* 컨트롤러를 통해 댓글 작성 동작을 수행하는 서블릿으로 이동한다.
* 게시글을 작성할 때와 마찬가지로 폼태그로 받아온 파라메터들을 저장한 후 `DB`에 접속해서 `insert` 동작을 수행한다.
* 게시글 본문 페이지에서 게시글 번호와 페이지 번호 정보를 필요로 하기 때문에 `get`방식으로 함께 넘겨준다.

## BoardDAO.java - insertComment(dto)

```java
public void insertComment(CommentDTO dto)
{
    int lastNum = 0;

    try {
        con = getCon();
        
        // 게시글의 댓글 갯수 증가
        sql = "update cafe_board set comment_count = comment_count + 1 where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.executeUpdate();
			
        System.out.println("DAO : 댓글 갯수 증가 완료");
			
        // 새 글 번호 찾기
        sql = "select max(num) from cafe_comment";
        pstmt = con.prepareStatement(sql);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
            lastNum = rs.getInt(1) + 1;
			
        // 댓글 삽입
        sql = "insert into cafe_comment(num, post_num, id, content, commented_date, re_ref, re_lev, re_seq, ip)"
                + " values(?,?,?,?,now(),?,?,?,?)";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, lastNum);
        pstmt.setInt(2, dto.getPost_num());
        pstmt.setString(3, dto.getId());
        pstmt.setString(4, dto.getContent());
        pstmt.setInt(5, lastNum);
        pstmt.setInt(6, 0);
        pstmt.setInt(7, 0);
        pstmt.setString(8, dto.getIp());
			
        pstmt.executeUpdate();
			
        System.out.println("DAO : 댓글 삽입 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
}
```

* 게시글 삽입 때와 마찬가지로 삽입 연산을 수행한다.
* 댓글이 달릴 게시글의 댓글 갯수를 증가시킨다.<br><br>

<p align="center"><img src="../../assets/images/loadComments.png"></p>

* 댓글 삽입 연산이 완료된 후 다시 게시글 본문 페이지로 이동하면 새로 작성된 댓글을 확인할 수 있다.<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-5`
