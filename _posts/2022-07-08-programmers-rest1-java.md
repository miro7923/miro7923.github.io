---
title: Python) 프로그래머스. 나머지가 1이 되는 수 찾기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Programmers
tags:
    - Algorithm
    - Programmers
    - Math
    - Java
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/30/lessons/87389](https://school.programmers.co.kr/learn/courses/30/lessons/87389)<br><br>

# 문제
<hr>
* 자연수 n이 매개변수로 주어집니다. n을 x로 나눈 나머지가 1이 되도록 하는 가장 작은 자연수 x를 return 하도록 solution 함수를 완성해주세요. 답이 항상 존재함은 증명될 수 있습니다.<br><br>

# 제한
<hr>
* 3 ≤ n ≤ 1,000,000<br><br><br>

# 👀 풀이
<hr>

* 한동안 코테준비를 놓았다가 다시 시작해 보면서 프로그래머스의 레벨1부터 시작하기로 했다. 원하는 직무는 자바 개발이니까 자바로 코테를 연습하려 하는데 너~~~무 오랜만이라 그런지 풀이를 떠올리는데 의외로 오래 걸렸다...ㅠ 반성하자...
* 시간에 대한 제한이 없고 입력값의 최대치도 그리 큰 수가 아니라 완전탐색으로 풀었다.
* 입력으로 주어지는 값의 최소값이 3이니까 반복문으로 3의 나머지를 1로 만들 수 있는 2부터 시작해서 `n`보다 1 작을 때까지 증가시키며 모듈러 연산(%)을 사용해서 나머지를 구했다. 이 과정에서 나머지가 1이면 정답으로 저장하고 반복문을 탈출한다.

# 코드
<hr>

<script src="https://gist.github.com/miro7923/3e9869bda33ee95da0b89724c7f40456.js"></script>
