---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 22 - 게시판 이미지 썸네일 보여주는 기능 추가
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
* 게시글에 첨부된 이미지가 있으면 메인 화면에서 게시글 내용을 미리보기로 보여줄 때 이미지도 함께 보여주는 기능을 추가했다.
* 원본 이미지로 썸네일용 이미지를 만들어주는 `Thumbnailator` 라이브러리를 사용했다.

## Thumbnailator 라이브러리 다운로드 및 설치
* [https://search.maven.org/artifact/net.coobird/thumbnailator/0.4.17/jar](https://search.maven.org/artifact/net.coobird/thumbnailator/0.4.17/jar)
* 위 사이트에 접속해서 오른쪽 상단에 보이는 `Download` 버튼을 눌러 `jar`파일을 다운로드 한다.
* 다운받은 `thumbnailator-0.4.17.jar` 파일을 `WEB-INF/lib` 폴더에 넣는다.

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

import net.coobird.thumbnailator.Thumbnails;
import net.coobird.thumbnailator.name.Rename;

public class BoardWriteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        // .. 생략
		
        while ((part = mp.readNextPart()) != null)
        {
            //.. 생략
            
            else if (part.isFile() && name.equals("image"))
            {
                // 이미지 파일일 때
                // 이미지 저장 경로 지정
                File dir = new File(ctx.getRealPath("/images"));
                File originDir = new File(dir + File.separator + "origins"); // 원본 이미지 저장 경로
                File thumbnailDir = new File(dir + File.separator + "thumbnails"); // 썸네일 저장 경로
				
                // 경로가 없으면 생성
                if (!dir.isDirectory()) dir.mkdir();
                if (!originDir.isDirectory()) originDir.mkdir();
                if (!thumbnailDir.isDirectory()) thumbnailDir.mkdir();
				
                FilePart filePart = (FilePart) part;
                String file = filePart.getFileName();
                if (file != null)
                {
                    // 지정한 경로에 원본 이미지 파일 쓰기
                    filePart.writeTo(originDir);
                    dto.setImage(file);
					
                    // 원본 이미지 파일을 이용해서 썸네일을 만들어 저장
                    // 크기 지정 후 지정 경로에 저장
                    Thumbnails.of(new File(originDir.getPath() + File.separator + file))
                        .size(300, 400)
                        .toFiles(thumbnailDir, Rename.NO_CHANGE);
					
                    System.out.println("img name: "+file);
                }
                else 
                    System.out.println("image; name: " + name + "; EMPTY");
            }
            // .. 생략
        }
		
        // .. 생략
		
        return forward;
    }
}
```

* 새 게시글을 작성하는 서블릿에서 원본 이미지 외에도 썸네일을 만들어 저장하는 코드를 추가했다.
* 원본 이미지와 썸네일을 저장할 폴더를 각각 구분해서 업로드한다.

## BoardImgAction.java

```java
package com.project.cafe.board.action;

import java.awt.image.renderable.ParameterBlock;
import java.io.File;
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
        String originImgName = request.getParameter("img_name");
        String thumbnailName = request.getParameter("thumbnail");
        String imgName = null;
		
        // 서버에 업로드 된 폴더 찾기
        String savePath = null;
        if (originImgName != null)
        {
            savePath = "images/origins";
            imgName = originImgName;
        }
        else 
        {
            savePath = "images/thumbnails";
            imgName = thumbnailName;
        }
		
        // 서버에 업로드 된 파일 위치 계산
        ServletContext ctx = request.getServletContext();
        String downloadPath = ctx.getRealPath(savePath);
        System.out.println("download path: "+downloadPath);
		
        // 다운로드 할 이미지의 전체 경로 계산
        // 사용자 OS에 따라 연결자 구분
        String imgPath = downloadPath + File.separator + imgName;
        System.out.println("imgPath: "+imgPath);
		
        //.. 이하 생략
    }
}
```

* 썸네일을 불러오는 로직 또한 기존에 만들었던 이미지 로드 로직과 같기 때문에 기존 함수를 약간 수정했다.
* 파라미터로 받은 값에 따라 원본 이미지가 있는 폴더와 썸네일이 있는 폴더에 각각 접근해서 이미지를 불러온다.
* 하략한 코드는 [JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 21 - 게시물에 첨부된 이미지와 파일 다른 경로에 저장하기](https://miro7923.github.io/project%20log/cafe-project-21/) 에서 확인할 수 있다.

## main.jsp

```jsp
<section data-theme="_bgp">
  <div data-layout="_r" class="MOD_ARTICLEBLOCKS1">
    <div data-layout="al16 ec8" class="MOD_ARTICLEBLOCKS1_Cont">
    <h2>최신글</h2>
      <a href="" class="MOD_ARTICLEBLOCKS1_BlockLarge" id="mainHref1">
        <!-- 썸네일 보여주는 부분 -->
        <img id="thumbnail1" src="" class="MOD_ARTICLEBLOCKS1_Img" role="img" aria-label="alt text">
        <div class="MOD_ARTICLEBLOCKS1_Txt">
          <h3 class="MOD_ARTICLEBLOCKS1_Title" id="mainTitle1">Article Title</h3>
          <p class="MOD_ARTICLEBLOCKS1_Category" id="mainContent1">Category</p>
        </div>
      </a>
    </div>
    <div data-layout="al16 ch8 ec4" class="MOD_ARTICLEBLOCKS1_Cont">
      <a href="" class="MOD_ARTICLEBLOCKS1_BlockSmall" id="mainHref2">
        <!-- 썸네일 보여주는 부분 -->
        <img id="thumbnail2" src="" class="MOD_ARTICLEBLOCKS1_Img" role="img" aria-label="alt text">
        <div class="MOD_ARTICLEBLOCKS1_Txt">
          <h3 class="MOD_ARTICLEBLOCKS1_Title" id="mainTitle2">Article Title</h3>
          <p class="MOD_ARTICLEBLOCKS1_Category" id="mainContent2">Category</p>
        </div>
      </a>
    </div>
    <div data-layout="al16 ch8 ec4" class="MOD_ARTICLEBLOCKS1_Cont">
      <a href="#" class="MOD_ARTICLEBLOCKS1_BlockSmall" id="mainHref3">
        <!-- 썸네일 보여주는 부분 -->
        <img id="thumbnail3" src="" class="MOD_ARTICLEBLOCKS1_Img" role="img" aria-label="alt text">
        <div class="MOD_ARTICLEBLOCKS1_Txt">
          <h3 class="MOD_ARTICLEBLOCKS1_Title" id="mainTitle3">Article Title</h3>
          <p class="MOD_ARTICLEBLOCKS1_Category" id="mainContent3">Category</p>
        </div>
      </a>
    </div>
  </div>
</section>
```

* `img` 태그에 각각 `thumbnail1, 2, 3` 형식으로 `id`를 설정해 자바스크립트로 `src`를 세팅해 줄 것이다.

## main.js

```javascript
$(document).ready(function()
{
    getFeeds();
});

function getFeeds()
{
    $.ajax({
        type: 'POST',
        async: false,
        url: './GetFeed.bo',
        dataType: 'json',
        data: {
            'cnt': 3,
            'len': 70
        },
        success: function(data) {
            if (data != null)
            {
                for (var i = 0; i < data.length; i++)
                {
                    var titleId = '#mainTitle';
                    titleId += (i + 1);
                    $(titleId).html(data[i].title);

                    var contentId = '#mainContent';
                    contentId += (i + 1);
                    $(contentId).html(data[i].content);

                    var hrefId = '#mainHref';
                    hrefId += (i + 1);
                    $(hrefId).attr('href', './BoardContent.bo?num='+data[i].num+'&pageNum=1');
					
                    var thumbnailId = '#thumbnail';
                    thumbnailId += (i + 1);
                    if (data[i].image == '없음')
                        $(thumbnailId).attr('src', '');
                    else 
                        $(thumbnailId).attr('src', './BoardImgAction.bo?thumbnail='+data[i].image);
                }
            }
        }
    });
}
```

* `GetFeed.java`에서 가져온 데이터 중 이미지 정보가 있을 때에만 썸네일 이미지를 출력한다.

## GetFeed.java

```java
package com.project.cafe.board.action;

import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class GetFeed implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("ajax 시작_GetFeed - execute() 호출");
		
        BoardDAO dao = new BoardDAO();
        ArrayList<BoardDTO> list = dao.getPosts
            (Integer.parseInt(request.getParameter("cnt")), Integer.parseInt(request.getParameter("len")));
		
        // DB에서 가져온 데이터들을 json에 저장
        JSONArray feedList = new JSONArray();
        for (int i = 0; i < list.size(); i++)
        {
            JSONObject feed = new JSONObject();
            feed.put("num", list.get(i).getNum());
            feed.put("title", list.get(i).getTitle());
            feed.put("content", list.get(i).getContent());
            feed.put("image", list.get(i).getImage());
			
            feedList.add(feed);
        }
		
        // 클라이언트에게 데이터 보내기
        // 한글처리
        response.setCharacterEncoding("UTF-8");
        // json 데이터 넘김
        response.getWriter().print(feedList.toJSONString());
        response.getWriter().close();
		
        return null;
    }
}
```

* `GetFeed.java`에서는 `json`데이터를 넘긴다.<br><br>

<p align="center"><img src="../../assets/images/thumbnail.png"></p>

* 완성! 👏
* 첨부된 이미지가 있을 때에만 썸네일을 출력하고 없을 때엔 출력하지 않는다.<br><br>

# 출처
* [[JAVA] 이미지 리사이즈, 썸네일 만들기 - Thumbnails.of](https://nm-it-diary.tistory.com/66)<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-5`
