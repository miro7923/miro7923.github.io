---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 24 - 댓글 수정 기능 추가
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

# 진행상황 1
* 게시글에 달린 댓글을 수정하는 기능을 추가했다.

## boardContent.jsp

```jsp
<!-- 본인 글 일때만 수정/삭제 가능 -->
<span>
<%if (id != null && id.equals(coList.get(i).getId())) { %>
  <a href="javascript:void(0);" onclick="showCommentBox(<%=i %>);" id="modify">수정&nbsp;</a>
</span>
<span>
  <a href="#">삭제&nbsp;</a>
</span>
<% } %>
```

* 로그인 정보를 확인 후 본인 글 일때만 수정과 삭제를 할 수 있는 버튼이 보인다.

## boardContent.js

```javascript
function showCommentBox(idx)
{
    var con = document.getElementById('modify' + idx);
    var comBlock = document.getElementById('modifyComment' + idx);
    if (comBlock.style.display == 'none')
    {
        comBlock.style.display = 'inline-block';
        con.innerHTML = '취소';
    }
    else 
    {
        comBlock.style.display = 'none';
        con.innerHTML = '수정';
    }
}
```

* 수정 버튼을 누르면 댓글을 수정할 수 있는 창을 보여주거나 숨김 수 있게 제어한다.

## boardContent.jsp

```jsp
<p class="comContent" id="comContent<%=i%>">
  <%=coList.get(i).getContent() %>
</p>
<div style="display: none;" id="modifyComment<%=i%>">
  <form action="./CommentModifyAction.bo?num=<%=coList.get(i).getNum()%>&post_num=<%=coList.get(i).getPost_num()%>&pageNum=<%=pageNum%>" method="post" onsubmit="return writeComment();">
    <input type="hidden" name="id" value="<%=id%>">
    <textarea id="comment" name="comment" rows="10" cols="80" maxlength="500"><%=coList.get(i).getContent() %></textarea>
    <button type="submit" class="btn" id="commentBtn">입력</button>
  </form><br>
</div><br>
```

* 수정 버튼을 누르면 댓글 수정창이 나타나고 한 번 더 누르면 댓글 수정창이 사라진다.
* 댓글 수정 내용과 함께 댓글 번호와 게시글 정보도 함께 전달한다.

## CommentModifyAction.java

```java
package com.project.cafe.board.action;

import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.CommentDTO;

public class CommentModifyAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : CommentModifyAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("utf-8");
		
        // 파라메터 저장
        int num = Integer.parseInt(request.getParameter("num"));
        int post_num = Integer.parseInt(request.getParameter("post_num"));
        String pageNum = request.getParameter("pageNum");
        String comment = request.getParameter("comment");
		
        CommentDTO dto = new CommentDTO();
        dto.setNum(num);
        dto.setPost_num(post_num);
        dto.setContent(comment);
		
        System.out.println("M : commentDTO: "+dto);
		
        // DB 접속 및 update 실행
        BoardDAO dao = new BoardDAO();
        int result = dao.updateComment(dto);
		
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
        if (1 != result)
        {
            out.print("<script>");
            out.print("alert('잘못된 접근');");
            out.print("history.back();");
            out.print("</script>");
        }
		
        out.print("<script>");
        out.print("location.href='./BoardContent.bo?num="+post_num+"&pageNum="+pageNum+";'");
        out.print("</script>");
		
        return null;
    }
}
```

* `DB`에 접속해서 댓글 수정 동작을 수행한 뒤 결과에 따라 페이지를 이동한다.
* 로그인 한 본인만 수정할 수 있기 때문에 접근한 사람이 본인이 아닌 경우는 없을 것이라 생각하지만 혹시 모르니까  예외처리를 해 주었다.

## BoardDAO.java - updateComment(dto)

```java
public int updateComment(CommentDTO dto)
{
    int ret = -1;
		
    try {
        con = getCon();
			
        sql = "select * from cafe_comment where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, dto.getNum());
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            // 댓글이 존재하면 수정
            sql = "update cafe_comment set content=? where num=?";
            pstmt = con.prepareStatement(sql);
				
            pstmt.setString(1, dto.getContent());
            pstmt.setInt(2, dto.getNum());
				
            ret = pstmt.executeUpdate();
				
            System.out.println("DAO : 댓글 수정 완료");
        }
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

* 댓글이 존재하는지 확인한 후 수정한다.
* 모든 동작이 완료된 후 다시 게시글 본문 페이지로 이동하면 수정된 댓글을 확인할 수 있다.<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-3`
