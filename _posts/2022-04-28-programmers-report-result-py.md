---
title: Python) 프로그래머스. 신고 결과 받기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Programmers
tags:
    - Algorithm
    - Programmers
    - Implementation
    - Python
---

# 문제 링크
<hr>
* [https://programmers.co.kr/learn/courses/30/lessons/92334](https://programmers.co.kr/learn/courses/30/lessons/92334)<br><br>

# 문제
<hr>
* 신입사원 무지는 게시판 불량 이용자를 신고하고 처리 결과를 메일로 발송하는 시스템을 개발하려 합니다. 무지가 개발하려는 시스템은 다음과 같습니다.

* 각 유저는 한 번에 한 명의 유저를 신고할 수 있습니다.
* 신고 횟수에 제한은 없습니다. 서로 다른 유저를 계속해서 신고할 수 있습니다.
* 한 유저를 여러 번 신고할 수도 있지만, 동일한 유저에 대한 신고 횟수는 1회로 처리됩니다.
* k번 이상 신고된 유저는 게시판 이용이 정지되며, 해당 유저를 신고한 모든 유저에게 정지 사실을 메일로 발송합니다.
* 유저가 신고한 모든 내용을 취합하여 마지막에 한꺼번에 게시판 이용 정지를 시키면서 정지 메일을 발송합니다.

* 이용자의 ID가 담긴 문자열 배열 id_list, 각 이용자가 신고한 이용자의 ID 정보가 담긴 문자열 배열 report, 정지 기준이 되는 신고 횟수 k가 매개변수로 주어질 때, 각 유저별로 처리 결과 메일을 받은 횟수를 배열에 담아 return 하도록 solution 함수를 완성해주세요.<br><br>

# 제한
<hr>
* 2 ≤ id_list의 길이 ≤ 1,000
    * 1 ≤ id_list의 원소 길이 ≤ 10
    * id_list의 원소는 이용자의 id를 나타내는 문자열이며 알파벳 소문자로만 이루어져 있습니다.
    * id_list에는 같은 아이디가 중복해서 들어있지 않습니다.
* 1 ≤ report의 길이 ≤ 200,000
    * 3 ≤ report의 원소 길이 ≤ 21
    * report의 원소는 "이용자id 신고한id"형태의 문자열입니다.
    * 예를 들어 "muzi frodo"의 경우 "muzi"가 "frodo"를 신고했다는 의미입니다.
    * id는 알파벳 소문자로만 이루어져 있습니다.
    * 이용자id와 신고한id는 공백(스페이스)하나로 구분되어 있습니다.
    * 자기 자신을 신고하는 경우는 없습니다.
* 1 ≤ k ≤ 200, k는 자연수입니다.
* return 하는 배열은 id_list에 담긴 id 순서대로 각 유저가 받은 결과 메일 수를 담으면 됩니다.<br><br><br>

# 👀 풀이
<hr>
* 딕셔너리를 사용해서 풀었다.
* 이 문제에서 관리되어야 하는 것은 `id_list`의 원소별로 신고한 id, 각 id별 신고횟수이다.

1. `usersReport = {신고자 id : [신고된 id 리스트]}`와 `reportCnt = {신고된 id : 횟수}` 형태의 딕셔너리를 만든다.<br>
    1-1. `report`를 순회하면서 `usersReport`에 각 유저별로 신고한 id를 담는다.<br>
    1-2. `reportCnt`에 각 유저별로 신고된 횟수를 저장한다.<br>
    1-3. 이때 같은 유저가 동일한 아이디를 여러번 신고하지 않도록 예외처리를 해 주어야 한다.
2. `k`번 이상 신고된 아이디를 추출한 리스트 `stop = [k번 이상 신고된 id]`를 만든다.<br>
    2-1. `reportCnt`를 순회하며 `k`번 이상 신고된 아이디를 `stop`에 담는다.<br>
3. `id_list`를 순회하며 각 유저별 메일을 받은 횟수를 `answer`에 담는다.<br><br>
 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/0c297eece0eed5c168f5daf229ddb58e.js"></script><br><br>

* 그런데 이렇게 구구절절 써서 통과하고 보니까 아주 짧은 코드로 통과한 분이 계셨다...

[https://programmers.co.kr/learn/courses/30/lessons/92334/solution_groups?language=python3](https://programmers.co.kr/learn/courses/30/lessons/92334/solution_groups?language=python3)

* 믓지다...😦 `set`이 순간 떠오르긴 했으나 어떻게 활용할 지 몰라서 그냥 구구절절 코드를 썼는데 덕분에 하나 배울 수 있었다.
