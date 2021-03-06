---
title: Java) 프로그래머스 - 카카오프렌즈 컬러링북
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Programmers
tags:
    - Algorithm
    - Programmers
    - Java
    - BFS
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/30/lessons/1829](https://school.programmers.co.kr/learn/courses/30/lessons/1829)<br><br>

# 문제
<hr>
<p align="center"><img src="http://t1.kakaocdn.net/codefestival/apeach.png"></p>

* 출판사의 편집자인 어피치는 네오에게 컬러링북에 들어갈 원화를 그려달라고 부탁하여 여러 장의 그림을 받았다. 여러 장의 그림을 난이도 순으로 컬러링북에 넣고 싶었던 어피치는 영역이 많으면 색칠하기가 까다로워 어려워진다는 사실을 발견하고 그림의 난이도를 영역의 수로 정의하였다. (영역이란 상하좌우로 연결된 같은 색상의 공간을 의미한다.)
* 그림에 몇 개의 영역이 있는지와 가장 큰 영역의 넓이는 얼마인지 계산하는 프로그램을 작성해보자.
* 위의 그림은 총 12개 영역으로 이루어져 있으며, 가장 넓은 영역은 어피치의 얼굴면으로 넓이는 120이다.<br><br>

# 제한
<hr>
* 입력은 그림의 크기를 나타내는 m과 n, 그리고 그림을 나타내는 m × n 크기의 2차원 배열 picture로 주어진다. 제한조건은 아래와 같다.

* 1 <= m, n <= 100

* picture의 원소는 0 이상 2^31 - 1 이하의 임의의 값이다.
* picture의 원소 중 값이 0인 경우는 색칠하지 않는 영역을 뜻한다.<br><br><br>

# 👀 풀이
<hr>
* 예전에 풀었던 적이 있는 문제인데 자바로는 풀어본 적이 없어서 한 번 더 풀어보았다.
* `BFS` 기본 문제로 `BFS` 알고리즘을 영역별로 나눠서 탐색하도록 수정하면 된다.
* 그래서 [0][0] 인덱스부터 탐색하는 것이 아닌 2중 반복문으로 배열을 탐색하면서 색칠된 칸(숫자가 1 이상인 칸)부터 `BFS` 탐색을 시작해서 상하좌우에 더 이상 같이 색이 존재하지 않을 때까지 탐색하면 된다. 이 때 넓이도 함께 계산해서 최댓값을 계속 갱신해 주어야 한다.
* 하나의 영역 탐색이 끝나고 나면 영역의 갯수도 증가시켜 준다.<br><br>

# 코드
<hr>

<script src="https://gist.github.com/miro7923/7fd992e23df328368a5fd4b020ac6e79.js"></script>
