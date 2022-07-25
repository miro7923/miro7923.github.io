---
title: Java) 프로그래머스 - 정수 삼각형
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
    - DP
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/10302/lessons/62953](https://school.programmers.co.kr/learn/courses/10302/lessons/62953)<br><br>

# 문제
<hr>
<p align="center"><img src="https://grepp-programmers.s3.amazonaws.com/files/production/97ec02cc39/296a0863-a418-431d-9e8c-e57f7a9722ac.png"></p>

* 위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.
* 삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.<br><br>

# 제한
<hr>
* 삼각형의 높이는 1 이상 500 이하입니다.
* 삼각형을 이루고 있는 숫자는 0 이상 9,999 이하의 정수입니다.<br><br><br>

# 👀 풀이
<hr>
* 얼핏 보면 그리디로 풀 수 있을거 같지만 그럴 수 없다. 항상 큰 수를 고른다고 해서 무조건 합이 최대가 되는 것은 아니기 때문이다. 
* 문제의 조건대로 답을 구하려면 삼각형을 잘게 쪼개서 조건대로 더해보면 된다.
* 각 라인의 첫 번째와 마지막 칸엔 바로 윗 줄의 양 끝에 있는 값과 현재 칸의 값을 더한 값만 가능하고, 나머지는 대각선 방향에 있는 두 숫자와 더한 값 중 큰 값을 남기면 된다.<br><br>

# 하향식 풀이 코드
<hr>
<script src="https://gist.github.com/miro7923/82b721d9237427678d02b255168ff8a7.js"></script><br><br>

# 상향식 풀이 코드
<hr>
* 풀이 강의에서 일반적으로 생각할 수 있는 하향식 풀이가 아닌, 아래에서부터 위로 올라가는 상향식 풀이 방법도 보여주었다. 
* 이렇게 하면 각 라인의 끝 칸에 대해 예외처리를 해 줄 필요가 없다. 삼각형은 아래로 갈수록 넓어지기 때문이다. 

<script src="https://gist.github.com/miro7923/ff9c94c504efe2dc20582548a759ba64.js"></script>
