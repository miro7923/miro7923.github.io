---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 21 - 게시물에 첨부된 이미지와 파일 다른 경로에 저장하기
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
* 게시물에 이미지를 첨부하고 볼 수 있는 기능을 추가했다.
* 한 게시물에 이미지와 일반 파일을 함께 업로드 할 수 있게 했다. 그래서 이미지와 일반 파일을 다른 경로에 업로드할 것이다.
* `COS` 라이브러리를 사용했다.

## COS 라이브러리 다운로드 및 설치
* [http://www.servlets.com/](http://www.servlets.com/cos/)
* 위 사이트에 접속해서 하단에 `Download` 탭에 있는 압축파일을 다운로드 한다.
* 다운받은 파일 압축을 푼 후 `cos.jar` 파일을 `WEB-INF/lib` 폴더에 넣는다.

## boardWrite.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
    	<h2>게시글 작성</h2>
    	<form name="write" action="./BoardWriteAction.bo" method="post" enctype="multipart/form-data" onsubmit="return finalCheck();">
    	<input type="hidden" name="id" value="<%=id%>">
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">제목 </label><input type="text" name="title" id="title">
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_MsgField">내용 </label>
          <textarea id="MOD_TEXTFORM_MsgField" name="content"></textarea>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">이미지 등록 </label><input type="file" name="image" id="image" oninput="formatCheck();">
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">파일 등록 </label><input type="file" name="file" id="file">
        </div>
        <button type="submit" class="btn">글 등록</button>
      </form>
      </div>
  </div>
</section>
```

* 이미지 파일 등록도 `input` 태그의 `file` 속성을 사용한다.

## BoardContent.js

```javascript
function formatCheck()
{
    // 확장자 확인할 정규식
    var regex = new RegExp("(.*?)\.(jpg|jpeg|png|gif)");
    var maxSize = 10 * 1024 * 1024;
	
    var fileSize = $('#image')[0].files[0].size;
    if (fileSize > maxSize)
    {
        alert('5MB 이하만 첨부 가능합니다.');
        crossBrowsing();
    }
	
    if (!regex.test($('#image').val()))
    {
        alert('확장자가 jpeg, jpg, png, gif인 이미지 파일만 등록 가능합니다.');
        crossBrowsing();
    }
}

function crossBrowsing()
{
    var agent = navigator.userAgent.toLowerCase();
	
    // 크로스 브라우징 처리
    // IE일 때
    if (navigator.appName == 'Netscape' && navigator.userAgent.search('Trident') != -1 || agent.indexOf("msie") != -1)
        $('#image').replaceWith($('#image').clone(true));
    else // 그 외 브라우저
        $('#image').val('');	
}
```

* 이미지 파일 업로드를 시도하면 파일 크기와 확장자 확인을 통해 유효한 파일만 등록할 수 있게 한다.

## BoardWriteAction.java

```java
package com.project.cafe.board.action;

import java.io.File;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.oreilly.servlet.multipart.FilePart;
import com.oreilly.servlet.multipart.MultipartParser;
import com.oreilly.servlet.multipart.ParamPart;
import com.oreilly.servlet.multipart.Part;
import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardWriteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardWriteAction - execute() 호출");
		
        request.setCharacterEncoding("utf-8");
		
        BoardDTO dto = new BoardDTO();
		
        ServletContext ctx = request.getServletContext();
		
        // 파일의 저장크기
        int maxSize = 10 * 1024 * 1024; // 10MB
		
        MultipartParser mp = new MultipartParser(request, maxSize);
        mp.setEncoding("utf-8");
        Part part;
		
        while ((part = mp.readNextPart()) != null)
        {
            // form 태그로 저장된 파라미터를 읽어옴
            String name = part.getName();
			
            if (part.isParam())
            {
                // 파일이 아닐 때
                ParamPart param = (ParamPart)part;
                String value = param.getStringValue();
				
                System.out.println("param name :" + name + " value : " + value);
				
                // 각 파라미터에 맞춰 dto에 저장
                if (name.equals("id"))
                    dto.setId(value);
                else if (name.equals("title"))
                    dto.setTitle(value);
                else if (name.equals("content"))
                    dto.setContent(value);
            }
            else if (part.isFile() && name.equals("image"))
            {
                // 이미지 파일일 때
                // 이미지 저장 경로 지정
                File dir = new File(ctx.getRealPath("/images"));
				
                // 경로가 없으면 생성
                if (!dir.isDirectory()) dir.mkdir();
				
                FilePart filePart = (FilePart) part;
                String file = filePart.getFileName();
                if (file != null)
                {
                    // 지정한 경로에 파일 쓰기
                    filePart.writeTo(dir);
                    dto.setImage(file);
                    System.out.println("img name: "+file);
                }
                else 
                    System.out.println("image; name: " + name + "; EMPTY");
            }
            else if (part.isFile() && name.equals("file"))
            {
                // 일반 파일일 때
                File dir = new File(ctx.getRealPath("/upload"));
				
                // 경로 없으면 생성
                if (!dir.isDirectory()) dir.mkdir();
				
                FilePart filePart = (FilePart) part;
                String file = filePart.getFileName();
                if (file != null)
                {
                    filePart.writeTo(dir); // 지정한 경로에 파일 쓰기
                    dto.setFile(file);
                    System.out.println("file name: "+file);
                }
                else
                    System.out.println("file; name: " + name + "; EMPTY");
            }
        }
		
        // 사용자 ip주소 저장
        dto.setIp(request.getRemoteAddr());
        System.out.println("M : "+dto);
		
        // DB에 DTO 보내서 저장
        BoardDAO dao = new BoardDAO();
        dao.insertPost(dto);
		
        // 완료되면 글 목록 페이지로 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./BoardList.bo");
        forward.setRedirect(true);
		
        return forward;
    }
}
```

* 한 게시물에서 이미지와 일반파일 두 개가 업로드 되는데 파일 종류별로 경로를 나눠서 저장하고 싶었다.
* 한 폴더에 들어 있으면 정신없기도 하지만 종류별로 구분해 놓으면 이미지를 불러올 땐 이미지만 저장된 폴더에서 찾아서 가져오면 되니까 한꺼번에 섞인 폴더에서 찾아오는 것 보다는 빠르게 찾아올 수 있을 것이라 생각했기 때문이다.
* 그래서 `MultipartParser`를 사용해서 파라미터 이름별로 업로드 경로를 구분해서 파일을 업로드 한다.

## boardContent.jsp

```jsp
<tbody>
  <tr>
    <td colspan="5" style="white-space:pre-wrap; word-wrap:break-word; word-break: break-all;"><%=dto.getContent() %><br><br>
      <%if (dto.getImage() != null && !dto.getImage().equals("없음")) { %>
        <img src="./BoardImgAction.bo?img_name=<%=dto.getImage() %>"><br><br><br></td>
      <% } %>
  </tr>
  <tr>
    <td colspan="2">첨부파일</td>
    <td colspan="3">
      <!-- 첨부파일이 있을 때에만 하이퍼링크 연결 -->
      <%if (dto.getFile() == null || dto.getFile().equals("없음")) { %>
        <%=dto.getFile() %>
      <% }
        else { %>
        <a href="./BoardFileDownloadAction.bo?file_name=<%=dto.getFile() %>"><%=dto.getFile() %></a>
      <% } %>
    </td>
  </tr>
</tbody>
```

* 게시글 본문 페이지에 들어오면 첨부된 이미지가 있을 때에만 이미지를 불러온다.

## BoardImgAction.java

```java
package com.project.cafe.board.action;

import java.io.FileInputStream;
import java.net.URLEncoder;

import javax.servlet.ServletContext;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class BoardImgAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardImgAction - execute() 호출");
		
        // 전달된 이미지 파일 이름 저장
        String imgName = request.getParameter("img_name");
		
        // 서버에 업로드 된 폴더 찾기
        String savePath = "images";
		
        // 서버에 업로드 된 파일 위치 계산
        ServletContext ctx = request.getServletContext();
        String downloadPath = ctx.getRealPath(savePath);
        System.out.println("download path: "+downloadPath);
		
        // 다운로드 할 이미지의 전체 경로 계산
        // 사용자 OS에 따라 연결자 구분
        String imgPath = null;
        String agent = request.getHeader("User-Agent");
        if (agent.indexOf("Windows") != -1)
            imgPath = downloadPath + "\\" + imgName;
        else 
            imgPath = downloadPath + "/" + imgName;
		
        System.out.println("imgPath: "+imgPath);
		
        // 파일 저장 배열
        byte[] b = new byte[4096];
		
        // 데이터 주고받을 통로 생성
        FileInputStream fis = new FileInputStream(imgPath);
		
        // MIME 타입 정보 얻어오기
        String mimeType = ctx.getMimeType(imgPath);
        System.out.println("MIME type: "+mimeType);
		
        if (mimeType == null)
            mimeType = "application/octet-stream";
		
        // 전달받은 MIME 타입으로 브라우저 정보 설정
        response.setContentType(mimeType);
		
        // 인터넷 익스플로러 일 때 구분해서 처리
        boolean ieBrowser = (agent.indexOf("MISE") > -1 || agent.indexOf("Trident") > -1);
        if (ieBrowser)
            imgName = URLEncoder.encode(imgName, "utf-8").replaceAll("\\+", "%20");
        else 
            imgName = new String(imgName.getBytes("utf-8"), "iso-8859-1");
		
        // 응답정보 설정
        response.setHeader("Content-Disposition", mimeType);
		
        // 출력스트림 생성
        ServletOutputStream out = response.getOutputStream();
		
        int data = 0;
        while ((data = fis.read(b, 0, b.length)) != -1)
            out.write(b, 0, data);
		
        out.flush();
		
        out.close();
        fis.close();
		
        return null;
    }
}
```

* 여기까지 하면 게시글에 입장했을 때 업로드 한 이미지가 출력된다.<br><br><br>

# 출처
* [[JAVA] MultipartParser 파일 저장경로를 파라메터로 받아서 업로드하기](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=cacung82&logNo=220502809071)
* [Java 각각 다른 경로로 File Upload](https://velog.io/@jgkim0320/Java-%EA%B0%81%EA%B0%81-%EB%8B%A4%EB%A5%B8-%EA%B2%BD%EB%A1%9C%EB%A1%9C-File-Upload)
<br><br><br>

# 마감까지 
* `D-4`
