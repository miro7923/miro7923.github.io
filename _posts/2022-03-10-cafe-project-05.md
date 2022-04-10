---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 5 - 회원가입 유효성 검사 구현 완료
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
* 어제 회원가입 페이지에서 사용자가 입력한 아이디의 유효성을 검증하는 부분까지 구현했다.
* 그리고 오늘 회원가입 시 사용자가 입력한 정보가 유효한지 검증하는 로직을 모두 구현했다!

## 1. join.jsp

```html
<h3>회원가입</h3><br>
<form name="join" action="./MemberJoinAction.me" method="post" onsubmit="return finalCheck();">
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">아이디 </label><input type="text" name="id" id="id" placeholder="5~10자 이내의 영문+숫자 조합">
    </div>
    <div id="idMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">비밀번호 </label><input type="password" name="pass" id="pass" placeholder="8~20자 영문+숫자+특수문자 조합">
    </div>
    <div id="passMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">비밀번호 확인 </label><input type="password" name="confirm" id="confirm" placeholder="비밀번호를 다시 입력하세요.">
    </div>
    <div id="confirmMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">이름 </label><input id="MOD_TEXTFORM_NameField" type="text" name="name" class="name" placeholder="이름을 입력하세요.">
    </div>
    <div id="nameMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">생년월일 </label><input id="MOD_TEXTFORM_NameField" type="date" name="birth" class="birth">
    </div>
    <div id="birthMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">성별 </label><input class="radio" type="radio" name="gender" id="gender" value="남"><label class="radioText">남</label> 
    <input class="radio" id="gender" type="radio" name="gender" value="여"><label class="radioText">여</label>
    </div>
    <div id="genderMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">도시 </label>
        <select name="city">
            <option selected disabled>도시를 선택하세요.</option>
            <option>서울</option>
            <option>부산</option>
            <option>대전</option>
        </select>
    </div>
    <div id="cityMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_TelField">휴대폰 번호 </label>
    <label class="phone">
    <input id="phone1" type="tel" name="phone1" size="1" maxlength="3" oninput="tabCursor(1)"> - 
    <input id="phone2" type="tel" name="phone2" size="3" maxlength="4" oninput="tabCursor(2)"> - 
    <input id="phone3" type="tel" name="phone3" size="3" maxlength="4">
    </label>
    </div>
    <div id="phoneMsg"></div>
    <div class="formRow">
    <label for="MOD_TEXTFORM_EmailField">이메일 </label><input type="email" name="email" id="email" placeholder="abc123@gmail.com">
    </div>
    <div id="emailMsg"></div>
    <button type="submit" class="btn">Send form</button>
</form>
```

* `html` 부분은 무료 템플릿 사이트에 있는 것을 다운받아서 사용한 것이고 입력 정보 유효성 검사를 위해 각 필드별로 `id`와 `class`만 추가했다.
* 거주지를 입력하는 부분은 약식으로 도시 3개만 넣었다.
* `form` 태그를 최종 `submit` 하기 전에 `finalCheck()` 함수를 호출해 입력한 정보들의 유효성 검사를 완료한 후에 결과에 따라 페이지를 이동하도록 했다.

## 2. join.js

```javascript
var $id = false;
var $pass = false;
var $passConfirm = false;
var $name = false;
var $birth = false;
var $gender = false;
var $city = false;
var $phone = false;
var $email = false;
 
$(document).ready(function()
{
	idCheck();
	passCheck();
	passConfirm();
	nameCheck();
	birthCheck();
	emailCheck();
});

// 휴대폰 번호 필드 1, 2번 칸에서 지정된 숫자만큼 입력하면 다음 칸으로 커서 넘기는 함수
function tabCursor(section)
{
	var ph = 0;
	switch (section)
	{
		case 1:
			ph = $("#phone1").val();
			if (3 === ph.length)
				$("#phone2").focus();
			break;
		
		case 2:
			ph = $("#phone2").val();
			if (4 === ph.length)
				$("#phone3").focus();
			break;
	}
} 

function idCheck()
{
	$('#id').blur(function()
	{
		$.ajax({
			type: 'POST',
			async: false,
			url: './idCheck.me',
			data: {
				'id': $('#id').val()
			},
			dataType: 'text',
			success: function(data) {
				if (data === 'true')
				{
					var userId = $('#id').val();
					if (5 > userId.length || 10 < userId.length)
					{
						$id = false;
						$('#idMsg').text('5자리 이상 10자리 이하로 입력해 주세요.');
						$('#idMsg').css('color', 'red');
					}
					else 
					{
						$id = true;
						$('#idMsg').text('사용할 수 있는 아이디입니다.');
						$('#idMsg').css('color', 'green');
					}
				}
				else 
				{
					$id = false;
					$('#idMsg').text('이미 존재하는 아이디입니다.');
					$('#idMsg').css('color', 'red');
				}
			},
			error: function(data) {
				console.log('error');
			}
		});
	});
}

// 비밀번호 유효성 검사하는 함수
function passCheck()
{
	$('#pass').blur(function()
	{
		var pass = $('#pass').val();
		var num = pass.search(/[0-9]/g);
		var eng = pass.search(/[a-z]/ig);
		var spe = pass.search(/[`~!@@#$%^&*|₩₩₩'₩";:₩/?]/gi);

		if (8 > pass.length || 20 < pass.length)
		{
			$pass = false;
			$('#passMsg').text('8자 ~ 20자 이내로 입력해 주세요.');
			$('#passMsg').css('color', 'red');
		}
		else if (pass.search(/\s/) != -1)
		{
			$pass = false;
			$('#passMsg').text('공백 없이 입력해 주세요.');
			$('#passMsg').css('color', 'red');
		}
		else if (num < 0 || eng < 0 || spe < 0)
		{
			$pass = false;
			$('#passMsg').text('영문, 숫자, 특수문자를 포함해서 입력해 주세요.');
			$('#passMsg').css('color', 'red');
		}
		else 
		{
			$pass = true;
			$('#passMsg').text('사용 가능한 비밀번호 입니다!');
			$('#passMsg').css('color', 'green');
		}
	});
}

// 재입력한 비밀번호가 맞는지 확인하는 함수
function passConfirm()
{
	$('#confirm').blur(function() 
	{
		var pass = $('#confirm').val();

		if (0 >= pass.length)
		{
			$passConfirm = false;
			$('#confirmMsg').text('비밀번호를 한 번 더 입력해 주세요.');
			$('#confirmMsg').css('color', 'red');
		}
		else if ($('#pass').val() !== pass)
		{
			$passConfirm = false;
			$('#confirmMsg').text('비밀번호가 일치하지 않습니다.');
			$('#confirmMsg').css('color', 'red');
		}
		else
		{
			$passConfirm = true;
			$('#confirmMsg').text('비밀번호가 일치합니다!');
			$('#confirmMsg').css('color', 'green');
		}
	});
}

// 이름 입력 여부 확인하는 함수
function nameCheck()
{
	$('.name').blur(function()
	{
		var name = $('.name').val();

		if ("" === name)
		{
			$name = false;
			$('#nameMsg').text('이름을 입력해 주세요.');
			$('#nameMsg').css('color', 'red');
		}
		else 
		{
			$name = true;
			$('#nameMsg').text('');
		}
	});
}

// 생년월일 유효성 검사하는 함수
function birthCheck()
{
	// input 태그에서 date type으로 입력받아서 태그단에서 미리 유효한 날짜만 선택할 수 있게 하지만 첫 프로젝트라 연습 삼아 구현했다.
	$('.birth').blur(function()
	{
		var birth = $('.birth').val();
		
		var birthArr = birth.split('-');
		var year = birthArr[0];
		var month = birthArr[1];
		var day = birthArr[2];
		var today = new Date();
		var curYear = today.getFullYear();

		if (10 >= birth.length)
		{
			if (1900 > year || curYear < year) 
				$birth = false;
			else if (1 > month || 12 < month) 
				$birth = false;
			else if (1 > day || 31 < day) 
				$birth = false;
			else if (31 == day && (4 == month || 6 == month || 9 == month || 11 == month))
				$birth = false;
			else if (2 == month)
			{
				// 2월엔 윤년 여부 검사
				var leapYear = (0 == year % 4 && (0 != year % 100 || 0 == year % 400));
				if (29 < day || (29 == day && !leapYear))
					$birth = false;
				else 
					$birth = true;
			}
			else 
				$birth = true;
		}
		else 
			$birth = false;
	});
}

// 성별 선택 여부 확인하는 함수
function genderCheck()
{
	if ($('input[name=gender]').is(":checked"))
	{
		$gender = true;
		$('#genderMsg').text('');
	}	
	else
	{
		$gender = false;
		$('#genderMsg').text('성별을 선택하세요!');
		$('#genderMsg').css('color', 'red');	
	}
}

// 도시 선택 여부 확인하는 함수
function cityCheck()
{
	if ('0' == $('[name=city] > option:selected').val())
	{
		$city = false;
		$('#cityMsg').text("거주 중인 도시를 선택하세요!");
		$("#cityMsg").css("color", "red");
	}	
	else 
	{
		$city = true;
		$('#cityMsg').text('');
	}
}

// 휴대폰 번호 유효성 검사하는 함수
function phoneCheck()
{
	var first = $("#phone1").val();
	var second = $("#phone2").val();
	var third = $("#phone3").val();
	
	if ("010" == first && 4 == second.length && 4 == third.length)
	{
		$phone = true;
		$("#phoneMsg").text("");
	}	
	else 
	{
		$phone = false;
		$("#phoneMsg").text("휴대폰 번호 형식이 올바르지 않습니다!");
		$("#phoneMsg").css("color", "red");
	}
}

// 이메일 유효성 검사하는 함수
function emailCheck()
{
	$("#email").blur(function()
	{
		var email = $("#email").val();
		var regEmail = /^([0-9a-zA-Z_\.-]+)@([0-9a-zA-Z_-]+)(\.[0-9a-zA-Z_-]+){1,2}$/;
		if (regEmail.test(email))
		{
			$email = true;
			$("#emailMsg").text("");
		}
		else 
		{
			$email = false;
			$("#emailMsg").text("이메일 형식이 올바르지 않습니다!");
			$("#emailMsg").css("color", "red");
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
	
	if (!$email)
	{
		join.email.focus();
		return false;
	}
}
```

* 입력 데이터 중 생년월일은 `input` 태그에서 `type="date"`로 지정해서 입력 단계에서 유효성 검사가 이루어지긴 하는데 웹 개발 프로젝트는 처음 진행하는 것이라 연습 삼아 `String`형으로만 입력을 받았을 때에 유효성 검사를 확인하는 코드도 작성해 보았다. 사실상 필요 없는 부분이라 최종 빌드 때엔 삭제할 예정이다.<br><br><br>

# 마감까지 
* `D-25`
