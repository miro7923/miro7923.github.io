---
title: 프로젝트) Cafe(웹 사이트) 만들기 6 - 휴대폰 인증기능 구현
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
* tomcat 8.5<br><br><br>

# 시작
* 2022.3.4 ~ <br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
* `openAPI` 사용법을 익히고 싶어서 휴대폰 번호 인증하기 기능을 만들어 보았다.
* 그동안 `openAPI` 쓰면 쉽게 만들 수 있다, 개발이 편해진다 하는 말을 참 많이 들었는데 직접 해 보니까 쉽지 않아서 많이 헤멨다... 😔 처음 해 보는 것이라 그런걸까? 
* 그리고 아직 `Spring framework`는 배우지 않은 상태라서 `Java`와 `Servlet`을 이용해 `MVC Model2` 패턴으로 구현해야 하는데 대부분의 자료는 `Spring framework`를 이용한 것이라 더 어렵게 느껴졌던 것 같다. 하지만 지금까지는 코드로 일일이 구현해 왔던 것을 `Spring`에서는 어노테이션으로 처리할 수 있는 것을 보니까 빨리 `Spring`을 배워 보고 싶어졌다.
* 그래도 이번 구현을 통해 휴대폰 번호 인증 로직이 어떻게 돌아가는 지 알게 되었고 `openAPI`도 어떤 방식으로 사용하는지 어느 정도 알게 되어서 좋은 경험이었다고 생각한다.

## 1. 네이버 클라우드 회원가입 하기
* 대부분의 휴대폰 번호 인증 API는 유료였는데 네이버 클라우드의 API는 한 달에 50건까지 무료라서 사용했다.<br>

* [NAVER CLOUD PlATFORM[SENS API] - 네이버 문자 인증](https://giron.tistory.com/75)
* 기본적인 프로젝트 생성 방법은 이 블로그 글을 참고했다.
* 처음엔 `Products & Services` 탭에서 활성화 된 메뉴가 아주 적을 수 있는데 결제 수단을 등록해 주면 모든 메뉴를 볼 수 있다.

## 2. join.jsp에 휴대폰 번호 인증란 추가

```html
<!-- join.jsp -->

...
생략

<div class="formRow">
    <label for="MOD_TEXTFORM_TelField">휴대폰 번호 </label>
    <label class="phone">
    <input id="phone1" type="tel" name="phone1" size="1" maxlength="3" oninput="tabCursor(1)"> - 
    <input id="phone2" type="tel" name="phone2" size="3" maxlength="4" oninput="tabCursor(2)"> - 
    <input id="phone3" type="tel" name="phone3" size="3" maxlength="4">
    <button id="requestBtn" class="btn" type="submit" name="requestBtn">휴대폰인증</button>
    </label>
</div>
<div id="phoneMsg"></div>
<div class="formRow">
    <label for="MOD_TEXTFORM_EmailField">인증번호 </label>
    <label class="phone">
    <input type="text" name="validateNum" id="validateNum">
    <button id="validateBtn" class="btn" type="submit" name="validateBtn">인증번호 확인</button>
    </label>
</div>
<div id="validateMsg"></div>

...
생략
```

## 3. 휴대폰 번호 인증을 처리하는 클래스 만들기
* [네이버 클라우드 플랫폼 SMS API 사용법 전체 코드, 예제 코드, Java, Spring, 스프링, Simple & Easy Notification Service, SENS](https://bemind.tistory.com/66)
* 이 블로그 글을 참고해서 구현했다. 지금까지 수업에서 `JSON`을 배운 적이 없어서 처음 `API` 문서를 봤을 때 굉장히 난감했는데 이 글 덕분에 코드를 분석하며 `JSON`을 무슨 용도로, 어떻게 사용하는 지 알 수 있었다.<br>

* [[이렇게 사용하세요!] SMS 문자 메시지 발송 앱 사용하기 (SENS API 활용법)](https://blog.naver.com/PostView.naver?blogId=n_cloudplatform&logNo=222475388473&parentCategoryNo=&categoryNo=12&viewDate=&isShowPopularPosts=false&from=postView)
* API의 각 항목에 대한 가이드는 공식 블로그를 참고했다. 여기만 확인해도 서비스 아이디가 뭔지, uri는 또 무슨 용도인지... 등등을 알 수 있다.

```java
// SmsService.java

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;
import java.net.HttpURLConnection;
import java.net.URL;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.Base64;
import java.util.Random;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class SmsService 
{
    private String makeSignature(String method, String url, String timestamp, String accessKey, String secretKey) throws UnsupportedEncodingException, NoSuchAlgorithmException, InvalidKeyException
    {
        String space = " ";					// one space
        String newLine = "\n";					// new line
		
        String message = new StringBuilder()
            .append(method)
            .append(space)
            .append(url)
            .append(newLine)
            .append(timestamp)
            .append(newLine)
            .append(accessKey)
            .toString();
		
        SecretKeySpec signingKey;
        String encodeBase64String;
        try {
            signingKey = new SecretKeySpec(secretKey.getBytes("UTF-8"), "HmacSHA256");
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);
            byte[] rawHmac = mac.doFinal(message.getBytes("UTF-8"));
            encodeBase64String = Base64.getEncoder().encodeToString(rawHmac);
        }
        catch (Exception e) {
            encodeBase64String = e.toString();
        }
		
    return encodeBase64String;
}
	
//	{
//    "type":"(SMS | LMS | MMS)", // sms 타입
//    "contentType":"(COMM | AD)", // 메세지 타입
//    "countryCode":"string", // 국가번호
//    "from":"string",		// 발신번호
//    "subject":"string",		// 기본 메시지 제목
//    "content":"string",		// 기본 메시지 내용
//    "messages":[			// 메시지 정보(Object) - 최대 1000개
//        {
//            "to":"string",	// 수신번호(String)
//            "subject":"string", // 개별 메시지 제목(String)
//            "content":"string"  // 개별 메시지 내용(String)
//        }
//    ]
//}
	
    @SuppressWarnings("unchecked")
    public int sendSms(String dstPhoneNumber)
    {
        System.out.println("sendSms() 호출");
		
        String method = "POST";					// method
        String smsUrl = "https://sens.apigw.ntruss.com";	// url (include query string)
        String requestUrl = "/sms/v2/services/";
        String requestUrlType = "/messages";
        String accessKey = "{user access key id}";			// access key id (from portal or Sub Account)
        String secretKey = "{user secret key}";
        String serviceId = "{user service id}";
        String timestamp = Long.toString(System.currentTimeMillis());			// current timestamp (epoch)
		
        requestUrl += serviceId + requestUrlType;
        String apiUrl = smsUrl + requestUrl;
		
        JSONObject bodyJson = new JSONObject();
        JSONObject toJson = new JSONObject();
        JSONArray toArr = new JSONArray();
		
        toJson.put("to", dstPhoneNumber);
        toArr.add(toJson);
		
        bodyJson.put("type", "SMS");
        bodyJson.put("contentType", "COMM");
        bodyJson.put("countryCode", "82");
        bodyJson.put("from", "01029775074");
        bodyJson.put("subject", "[Web 발신]");

        // 난수 생성
        int min = 1000;
        int max = 9999;
        int validateNum = (int) (Math.random() * (max - min + 1)) + min;
        bodyJson.put("content", "인증번호 : " + Integer.toString(validateNum));
        System.out.println("validateNum : " + validateNum);
		
        bodyJson.put("messages", toArr);
		
        String body = bodyJson.toString();
		
        System.out.println("body : " + body);
		
        try {
            URL url = new URL(apiUrl);
			
            HttpURLConnection con = (HttpURLConnection) url.openConnection();
            con.setUseCaches(false);
            con.setDoOutput(true);
            con.setDoInput(true);
            con.setRequestProperty("Content-Type", "application/json");
            con.setRequestProperty("x-ncp-apigw-timestamp", timestamp);
            con.setRequestProperty("x-ncp-iam-access-key", accessKey);
            con.setRequestProperty("x-ncp-apigw-signature-v2", makeSignature(method, requestUrl, timestamp, accessKey, secretKey));
            con.setRequestMethod(method);
            con.setDoOutput(true);
            DataOutputStream dos = new DataOutputStream(con.getOutputStream());
			
            dos.write(body.getBytes());
            dos.flush();
            dos.close();
			
            int responseCode = con.getResponseCode();
            BufferedReader br;
            System.out.println("responseCode : " + responseCode);
            if (202 == responseCode)
            {
                // 정상호출
                br = new BufferedReader(new InputStreamReader(con.getInputStream()));
            }
            else 
            {
                // 에러발생
                br = new BufferedReader(new InputStreamReader(con.getErrorStream()));
            }
			
            String inputLine;
            StringBuffer response = new StringBuffer();
            while (null != (inputLine = br.readLine()))
                response.append(inputLine);
			
            br.close();
			
            System.out.println(response.toString());
        }
        catch (Exception e) {
            System.out.println(e);
        }
		
        System.out.println("sendSms() 끝");
		
        return validateNum;
    }
}
```

* 난수를 발생시켜서 인증번호를 만들었으며 사용자가 입력한 번호와 비교하기 위해서 생성된 난수를 리턴하도록 했다.
* 난수는 4자리 숫자를 만들도록 범위를 지정해 주었다.

## 4. 문자 전송 결과를 받아올 서블릿 생성

```java
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/validatePhone.me")
public class ValidatePhone extends HttpServlet 
{
	protected void doProcess(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		resp.setContentType("text/html; charset=utf-8");
		
		SmsService smsService = new SmsService();
		PrintWriter out = resp.getWriter();
		
		int validateNum = smsService.sendSms(req.getParameter("phone"));
		out.print(validateNum);
		
		out.close();
	}
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		doProcess(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		doProcess(req, resp);
	}
}
```

* 문자 전송 후 발생된 난수를 받아와 출력 스트림을 이용해 `response` 객체로 전달하도록 했다.

## 5. join.js에서 join.jsp와 인증 페이지를 연결하는 코드 작성

```javascript
...
생략

var $validateNum;
var $checkValidate = false;
 
$(document).ready(function()
{
    ...
    중략
    
    getToken();
    checkValidateNum();
});

function getToken()
{
    $('#requestBtn').click(function()
    {
        phoneCheck();
        if ($phone)
        {
            var phoneNum = $('#phone1').val() + $('#phone2').val() + $('#phone3').val();
            $.ajax({
                type: 'POST',
                async: false,
                url: './validatePhone.me',
                data: {
                    'phone': phoneNum
                },
                dataType: 'text',
                success: function(data)
                {
                    alert('인증번호 전송 완료!');
                    $validateNum = data;
                }
            });					
        }
    });
}

function checkValidateNum()
{
    $('#validateBtn').click(function()
    {
        var userInput = $("#validateNum").val();
        if ($validateNum === userInput)
        {
            $checkValidate = true;
            $("#validateMsg").text("인증이 완료되었습니다!");
            $("#validateMsg").css("color", "green");
            $("#validateBtn").prop("disabled", true);
            $("#requestBtn").prop("disabled", true);
            $("#validateBtn").css("background", "gray");
            $("#requestBtn").css("background", "gray");
        }
        else 
        {
            $checkValidate = false;
            $("#validateMsg").text("인증번호가 일치하지 않습니다.");
            $("#validateMsg").css("color", "red");
        }
    });
}

// 마지막 제출 전에 유효성 검사하는 함수
function finalCheck() 
{
    var join = document.join;
    if (!$id)
    {
        join.id.focus();
        return false;
    }
		
    if (!$pass)
    {
        join.pass.focus();
        return false;
    }

    if (!$passConfirm)
    {
        join.confirm.focus();
        return false;
    }

    if (!$name)
    {
        join.name.focus();
        return false;
    }

    if (!$birth)
    {
        join.birth.focus();
        return false;
    }
	
    genderCheck();
    if (!$gender)
        return false;

    cityCheck();
    if (!$city)
        return false;
	
    phoneCheck();
    if (!$phone)
    {
        join.phone1.focus();
        return false;
    }
	
    if (!$checkValidate)
    {
        join.validateNum.focus();
        return false;
    }
	
    if (!$email)
    {
        join.email.focus();
        return false;
    }
} 

...
생략
```

* 서블릿을 이용해 문자 전송 요청을 보내고 발생된 난수를 받아올 것이기 때문에 `Ajax`를 이용했다.
* 인증번호 확인 버튼을 누르면 서블릿에서 받아온 번호와 비교해서 일치하는지 아닌지 확인 후 결과를 알려준다.<br><br>

<p align="center"><img src="../../assets/images/validatePhone.png" width="400"></p><br><br>

# 오늘 얻은 팁
* 만약 css 변경 사항이 적용되지 않을 때에는 css 파일 경로 설정하는 부분의 맨 뒤에 `?`를 붙여주면 된다.

```css
<link rel="stylesheet" href="${pageContext.request.contextPath}/css/modules.css?after">
```

* 이런 식으로 맨 뒤에 `?`를 붙이고 다음에는 아무 말이나 입력해도 되고 입력하지 않아도 된다.

* 이제 로그인 기능 만들어야지... <br><br><br>

# 마감까지 
* `D-25`
