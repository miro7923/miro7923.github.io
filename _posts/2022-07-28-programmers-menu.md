---
title: Java) 프로그래머스 - 메뉴 리뉴얼
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
    - Combination
    - BruteForce
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/30/lessons/72411](https://school.programmers.co.kr/learn/courses/30/lessons/72411)<br><br>

# 문제
<hr>
* 각 손님들이 주문한 단품메뉴들이 문자열 형식으로 담긴 배열 orders, "스카피"가 추가하고 싶어하는 코스요리를 구성하는 단품메뉴들의 갯수가 담긴 배열 course가 매개변수로 주어질 때, "스카피"가 새로 추가하게 될 코스요리의 메뉴 구성을 문자열 형태로 배열에 담아 return 하도록 solution 함수를 완성해 주세요.
* 전문은 링크에...<br><br>

# 제한
<hr>
* orders 배열의 크기는 2 이상 20 이하입니다.
* orders 배열의 각 원소는 크기가 2 이상 10 이하인 문자열입니다.
    * 각 문자열은 알파벳 대문자로만 이루어져 있습니다.
    * 각 문자열에는 같은 알파벳이 중복해서 들어있지 않습니다.
* course 배열의 크기는 1 이상 10 이하입니다.
    * course 배열의 각 원소는 2 이상 10 이하인 자연수가 오름차순으로 정렬되어 있습니다.
    * course 배열에는 같은 값이 중복해서 들어있지 않습니다.
* 정답은 각 코스요리 메뉴의 구성을 문자열 형식으로 배열에 담아 사전 순으로 오름차순 정렬해서 return 해주세요.
    * 배열의 각 원소에 저장된 문자열 또한 알파벳 오름차순으로 정렬되어야 합니다.
    * 만약 가장 많이 함께 주문된 메뉴 구성이 여러 개라면, 모두 배열에 담아 return 하면 됩니다.
    * orders와 course 매개변수는 return 하는 배열의 길이가 1 이상이 되도록 주어집니다.<br><br><br>

# 👀 풀이
<hr>
* 푸는데 참 오래걸렸다...ㅎ
* 처음엔 하나씩 조합해서 주문목록에서 찾는 방식으로 해야하나 하는 막연한 생각이 들었지만 뭔가 엄두가 안나서 미루다가... 질문 게시판을 참고했다. 참고 결과 처음에 생각했던 방향이 어느정도 맞아 보여서 이 아이디어로 코드를 짜 보았다.

1. 먼저 주문에 사용된 알파벳들을 조합해서 메뉴 구성을 짜 보아야 한다. 그러기 위해서는 손님들의 주문에 어떤 알파벳들이 쓰였는지 알아야 한다. 이걸 알아내기 위해 `Set`을 이용해 주문목록을 순회하면서 사용된 알파벳들을 추출한다. `Set`은 중복을 허용하지 않기 때문에 주문에 사용된 알파벳들을 알아낼 수 있다.
2. 쓰인 알파벳들을 알아냈으면 이걸 오름차순으로 정렬한다. 정답은 오름차순으로 조합된 결과여야 하기 때문에 알파벳들을 오름차순으로 정렬한 다음 조합을 시작할 것이다.
3. `course`의 원소값만큼 길이로 알파벳들을 조합한 결과를 `Map<String, Integer>`에 담는다. 왜냐면 각 조합별로 몇 번 주문되었는지 셀 것이기 때문이다. `Menu` 클래스를 만들어 조합 문자열과 주문횟수를 저장한 뒤 배열에 담을수도 있는데 나중에 찾을 때 오래 걸릴 거 같아서 키값으로 빠르게 찾아오려고 `Map`을 사용했다. 하지만 지금 글을 쓰면서 생각해보니 클래스를 담은 배열로도 충분할 거 같은데 지금은 시간이 늦었으니 내일 리팩토링을 해 보는 것으로!
    * ➡️ 맵을 사용했던 부분을 전부 클래스를 배열로 바꿔서 테스트 해 본 결과 작동이 잘 된다!
    * 조합 결과를 담을 때 <메뉴조합, 0> 페어로 담는다.
4. 조합별로 몇 번 사용되었는지 횟수를 센다. 
5. 이 중에서 2번 이상 주문된 조합만 정답이 될 가능성이 있다. 그런데 문제의 조건이 각 `course`별 메뉴 조합 중에서는 가장 많이 주문된 조합을 리턴하고 가장 많이 주문된 조합이 여러개라면 여러개를 다 리턴하는 것이다.(처음에 이걸 못 알아먹어서 헤멤..) 그래서 먼저 2번 이상 주문된 조합만 추출한다.
6. 5번에서 추출한 메뉴들 중에서 각 `course` 갯수별로 가장 많이 주문된 조합을 찾아서 정답 배열에 담아서 리턴한다.

* 글로 과정을 정리하니까 간단해 보이는데 미처 생각치 못한 예외케이스들이 있어 디버깅에 시간이 오래 걸렸다. 하지만 예전같았음 진작에 답지 보고 풀었을텐데 끝까지 내가 풀어서 뿌듯하다!<br><br>

# 코드
<hr>

## HashMap 사용한 코드

<script src="https://gist.github.com/miro7923/7d6b28ff671a62dfa9d5d7561f729b58.js"></script>

## ArrayList + Menu class 사용한 코드

<script src="https://gist.github.com/miro7923/10bb71c7eae5eb929a8000a481b430ed.js"></script>
