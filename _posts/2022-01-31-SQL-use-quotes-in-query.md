---
title: 쿼리구문에 큰 따옴표 사용 문법
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Database
tags:
    - DB
---
# 쿼리구문에 큰 따옴표(" ")가 사용되는 경우
* `컬럼명` [AS] "Alias" 
    * 대소문자 구분
    * 특수문자 포함
    * 공백 포함하는 경우
<br><br>
* `TO_CHAR(sysdate, 'YYYY-MM-DD "Time" HH24:MI')`
    * 사용자 형식 내 문자열 포함 시

# => 긴가민가하면 작은 따옴표를 쓰자
