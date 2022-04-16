---
title: Python) 이것이 취업을 위한 코딩테스트다 12 구현문제 8. 문자열 재정렬
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - AlgorithmStudy
tags:
    - Algorithm
    - Implementation
    - Python
---

# 문제 출처
<hr>
* 이것이 취업을 위한 코딩테스트다 with 파이썬
* Chapter 12 - 구현문제 8. 문자열 재정렬<br><br>
 
# 제한
<hr>
* 시간 제한 : 1 초
* 메모리 제한 : 128 MB<br><br>

# 문제
<hr>
* 알파벳 대문자와 숫자(0 ~ 9)로만 구성된 문자열이 입력으로 주어진다. 이때 모든 알파벳을 오름차순으로 정렬하여 이어서 출력한 뒤에, 그 뒤에 모든 숫자를 더한 값을 이어서 출력한다.<br><br>

# 입력
<hr>
* 첫째 줄에 하나의 문자열 S가 주어진다. (1 <= S의 길이 <= 10,000)<br><br>

# 출력
<hr>
* 첫째 줄에 문제에서 요구하는 정답을 출력한다.<br><br><br>

# 👀 풀이
<hr>
* 입력받은 문자열을 순회하면서 숫자와 문자를 따로 저장한다.
* 문자를 따로 저장한 문자열 뒤에 숫자들의 합을 더해서 출력하면 정답을 만들 수 있다.<br><br>

 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/fe56f87d393663b2c29bc35f8973d7b1.js"></script>
