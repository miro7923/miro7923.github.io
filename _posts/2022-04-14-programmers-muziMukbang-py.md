---
title: Python) 프로그래머스. 무지의 먹방 라이브
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - BOJ
tags:
    - Algorithm
    - Programmers
    - Greedy
    - Python
---

# 문제 링크
<hr>
* [https://programmers.co.kr/learn/courses/30/lessons/42891](https://programmers.co.kr/learn/courses/30/lessons/42891)<br><br>

# 문제
<hr>
* 평소 식욕이 왕성한 무지는 자신의 재능을 뽐내고 싶어 졌고 고민 끝에 카카오 TV 라이브로 방송을 하기로 마음먹었다.

* 그냥 먹방을 하면 다른 방송과 차별성이 없기 때문에 무지는 아래와 같이 독특한 방식을 생각해냈다.

* 회전판에 먹어야 할 N 개의 음식이 있다.
* 각 음식에는 1부터 N 까지 번호가 붙어있으며, 각 음식을 섭취하는데 일정 시간이 소요된다.
* 무지는 다음과 같은 방법으로 음식을 섭취한다.

* 무지는 1번 음식부터 먹기 시작하며, 회전판은 번호가 증가하는 순서대로 음식을 무지 앞으로 가져다 놓는다.
* 마지막 번호의 음식을 섭취한 후에는 회전판에 의해 다시 1번 음식이 무지 앞으로 온다.
* 무지는 음식 하나를 1초 동안 섭취한 후 남은 음식은 그대로 두고, 다음 음식을 섭취한다.
* 다음 음식이란, 아직 남은 음식 중 다음으로 섭취해야 할 가장 가까운 번호의 음식을 말한다.
* 회전판이 다음 음식을 무지 앞으로 가져오는데 걸리는 시간은 없다고 가정한다.
* 무지가 먹방을 시작한 지 K 초 후에 네트워크 장애로 인해 방송이 잠시 중단되었다.
* 무지는 네트워크 정상화 후 다시 방송을 이어갈 때, 몇 번 음식부터 섭취해야 하는지를 알고자 한다.
* 각 음식을 모두 먹는데 필요한 시간이 담겨있는 배열 food_times, 네트워크 장애가 발생한 시간 K 초가 매개변수로 주어질 때 몇 번 음식부터 다시 섭취하면 되는지 return 하도록 solution 함수를 완성하라.<br><br>

# 제한
* food_times 는 각 음식을 모두 먹는데 필요한 시간이 음식의 번호 순서대로 들어있는 배열이다.
* k 는 방송이 중단된 시간을 나타낸다.
* 만약 더 섭취해야 할 음식이 없다면 -1을 반환하면 된다.

# 입력
<hr>
* food_times 의 길이는 1 이상 2,000 이하이다.
* food_times 의 원소는 1 이상 1,000 이하의 자연수이다.
* k는 1 이상 2,000,000 이하의 자연수이다.
* food_times 의 길이는 1 이상 200,000 이하이다.
* food_times 의 원소는 1 이상 100,000,000 이하의 자연수이다.
* k는 1 이상 2 x 10^13 이하의 자연수이다.<br><br><br>

# 👀 풀이
<hr>
* 적절한 풀이가 떠오르지 않아서 해설을 참고했다.
* 모든 음식을 시간 기준으로 오름차순 정렬한 뒤에 시간이 적게 걸리는 음식부터 제거해 나가는 것이 기본 아이디어<br><br>
 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/aa7a53e5e8b3997356df015841a1d1ff.js"></script>