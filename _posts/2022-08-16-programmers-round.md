---
title: Java) 프로그래머스 - 예상 대진표
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
    - implementation
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/30/lessons/12985](https://school.programmers.co.kr/learn/courses/30/lessons/12985)<br><br>

# 문제
<hr>
* △△ 게임대회가 개최되었습니다. 이 대회는 N명이 참가하고, 토너먼트 형식으로 진행됩니다. N명의 참가자는 각각 1부터 N번을 차례대로 배정받습니다. 그리고, 1번↔2번, 3번↔4번, ... , N-1번↔N번의 참가자끼리 게임을 진행합니다. 각 게임에서 이긴 사람은 다음 라운드에 진출할 수 있습니다. 이때, 다음 라운드에 진출할 참가자의 번호는 다시 1번부터 N/2번을 차례대로 배정받습니다. 만약 1번↔2번 끼리 겨루는 게임에서 2번이 승리했다면 다음 라운드에서 1번을 부여받고, 3번↔4번에서 겨루는 게임에서 3번이 승리했다면 다음 라운드에서 2번을 부여받게 됩니다. 게임은 최종 한 명이 남을 때까지 진행됩니다.

* 이때, 처음 라운드에서 A번을 가진 참가자는 경쟁자로 생각하는 B번 참가자와 몇 번째 라운드에서 만나는지 궁금해졌습니다. 게임 참가자 수 N, 참가자 번호 A, 경쟁자 번호 B가 함수 solution의 매개변수로 주어질 때, 처음 라운드에서 A번을 가진 참가자는 경쟁자로 생각하는 B번 참가자와 몇 번째 라운드에서 만나는지 return 하는 solution 함수를 완성해 주세요. 단, A번 참가자와 B번 참가자는 서로 붙게 되기 전까지 항상 이긴다고 가정합니다.<br><br>

# 제한
* N : 21 이상 220 이하인 자연수 (2의 지수 승으로 주어지므로 부전승은 발생하지 않습니다.)
* A, B : N 이하인 자연수 (단, A ≠ B 입니다.)<br><br>

# 👀 풀이
<hr>
* 반례를 충분히 생각치 못해서 시간이 좀 걸린 문제인데 접근법 자체는 쉽게 생각해 낼 수 있었다.

1. 문제를 보면 매 라운드마다 앞뒤 홀짝수 번호끼리 붙게 된다. 하지만 2,3번은 붙을 수 없고 `1,2번 / 3,4번`과 같은 페어끼리만 붙는다. 그러면 `a = 1, b = 2`일 때 서로 붙게 된다.<br>
    1-1. 여기서 a와 b 두 수의 차가 1인데 뒷번호인 참가자의 현재 번호가 짝수라면 둘은 현재 라운드에서 만나게 될 것이라는 것을 알 수 있다.
2. 두 참가자 중 번호가 빠른 사람과 늦은 사람을 구별한 뒤 둘의 번호를 감소시켜가며 만나게 될 라운드를 구한다. 다음 라운드 번호를 구할 때 현재 번호가 짝수라면 그냥 2로 나누면 다음 번호를 구할 수 있고, 홀수라면 2로 나눈 몫에 1을 더해주어야 정확한 다음 번호를 구할 수 있다.<br><br>

# 자기 성찰
* 아무 생각없이 항상 a가 앞 번호이고 b가 뒷 번호라는 전제하에 코드를 짰는데 사실 문제엔 그런 말은 없었다.(그래서 계속 반만 맞음 ㅠㅠ) 문제의 조건을 잘 읽어보고 여러 방향으로 반례를 생각해서 코드를 짜자!<br><br>

# 코드
<hr>

<script src="https://gist.github.com/miro7923/605384e0d37df3fe05b4151df040f879.js"></script>
