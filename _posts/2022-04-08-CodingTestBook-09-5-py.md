---
title: Python) 이것이 취업을 위한 코딩테스트다 09 실전문제 3. 전보
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - BOJ
tags:
    - Algorithm
    - Graph
    - Python
---

# 문제 출처
<hr>
* 이것이 취업을 위한 코딩테스트다 with 파이썬
* Chapter 09 - 실전문제 3. 전보<br><br>
 
# 제한
<hr>
* 시간 제한 : 1 초
* 메모리 제한 : 128 MB<br><br>

# 문제
<hr>
* 어떤 나라에는 N개의 도시가 있다. 그리고 각 도시는 보내고자 하는 메시지가 있는 경우, 다른 도시로 전보를 보내서 다른 도시로 해당 메시지를 전송할 수 있다. 하지만 X라는 도시에서 Y라는 도시로 전보를 보내고자 한다면, 도시 X에서 도시 Y로 향하는 통로가 설치되어 있어야 한다. 예를 들어 X에서 Y로 향하는 통로는 있지만, Y에서 X로 향하는 통로가 없다면 Y는 X로 메시지를 보낼 수 없다. 또한 통로를 거쳐 메시지를 보낼 때에는 일정 시간이 소요된다.
* 어느 날 C라는 도시에서 위급 상황이 발생했다. 그래서 최대한 많은 도시로 메시지를 보내고자 한다. 메시지는 도시 C에서 출발하여 각 도시 사이에 설치된 통로를 거쳐, 최대한 많이 퍼져나갈 것이다. 각 도시의 번호와 통로가 설치되어 있는 정보가 주어졌을 때, 도시 C에서 보낸 메시지를 받게 되는 도시의 개수는 총 몇 개이며 도시들이 모두 메시지를 받는 데까지 걸리는 시간은 얼마인지 계산하는 프로그램을 작성하시오.<br><br>

# 입력
<hr>
* 첫째 줄에 도시의 개수 N, 통로의 개수 M, 메시지를 보내고자 하는 도시 C가 주어진다.(1 <= N <= 30,000, 1 <= M <= 200,000, 1 <= C <= N)
* 둘째 줄부터 M + 1번째 줄에 걸쳐서 통로에 대한 정보 X, Y, Z가 주어진다. 이는 특정 도시 X에서 다른 특정 도시 Y로 이어지는 통로가 있으며, 메시지가 전달되는 시간이 Z라는 의미이다.(1 <= X, Y <= N, 1 <= Z <= 1,000)<br><br>

# 출력
<hr>
* 첫째 줄에 도시 C에서 보낸 메시지를 받는 도시의 총 개수와 총 걸리는 시간을 공백으로 구분하여 출력한다.<br><br><br>

# 👀 풀이
<hr>
* 시작점이 정해져 있고 N의 범위가 꽤 커서 워셜 플로이드 보다는 다익스트라가 적합하다고 생각했고, 또 우선순위 큐를 사용하는 다익스트라를 쓰는 것이 좋겠다고 생각했다.
* 다익스트라 알고리즘으로 C 도시에서 다른 도시들로 이동하는 최단 거리를 찾아서 갱신한다.
* 마지막에 출력할 때 최단거리 테이블의 값이 무한대가 아닌 인덱스의 개수를 세고, 그 중에서 가장 먼 도시의 최단 거리를 출력하면 된다.
* 그런데 문제는 총 걸리는 시간을 출력하라고 되어 있어서 도착 가능한 도시들의 최단 거리의 합인가 싶어서 처음에는 저렇게 출력하니까 틀렸었다. 문제 설명 좀 명확했으면 좋겠다.
* 이동 가능한 도시의 개수를 셀 때엔 자기 자신도 포함되기 때문에 마지막 출력 시에는 1을 빼고 출력해야 한다.<br><br>

 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/a8681b5b930a425a7b7421636657bf8d.js"></script>
