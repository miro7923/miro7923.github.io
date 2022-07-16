---
title: Java) 프로그래머스 - 올바른 괄호의 갯수
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
    - DFS
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/10302/lessons/62952](https://school.programmers.co.kr/learn/courses/10302/lessons/62952)<br><br>

# 문제
<hr>
* 올바른 괄호란 (())나 ()와 같이 올바르게 모두 닫힌 괄호를 의미합니다. )(나 ())() 와 같은 괄호는 올바르지 않은 괄호가 됩니다. 괄호 쌍의 개수 n이 주어질 때, n개의 괄호 쌍으로 만들 수 있는 모든 가능한 괄호 문자열의 갯수를 반환하는 함수 solution을 완성해 주세요.<br><br>

# 제한
<hr>
* 괄호 쌍의 개수 N : 1 ≤ n ≤ 14, N은 정수<br><br><br>

# 👀 풀이
<hr>
* 풀이가.. 모르겠어서 풀이 영상을 봤다.
* DFS/BFS로 풀 수 있는 문제인데 강의에서는 DFS로 진행되었다. DFS로 쓴 코드를 BFS로 바꿔도 풀리는 문제이다. 

* 문제를 풀기 위해 괄호 쌍을 직접 맞출 필요는 없다. 
* 열린 괄호의 갯수와 닫힌 괄호의 갯수만 세면 쉽게 풀 수 있는 문제였다. 
* 문제의 조건대로 가능한 괄호 쌍을 만드려면 열린 괄호의 갯수와 닫힌 괄호의 갯수가 같으면 된다. 두 괄호의 갯수가 다르다면 완전히 닫히지 않은 괄호인 것이다. 스택을 이용해 괄호 쌍을 맞춰주면 문제를 풀 수 있다.

# 코드
<hr>
<script src="https://gist.github.com/miro7923/ed1a52980116a3ef6ff284c5cb8b5e8d.js"></script>
