---
title: Python) 프로그래머스. 로또의 최고 순위와 최저 순위
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
---

# 문제 링크
<hr>
* [https://school.programmers.co.kr/learn/courses/30/lessons/77484](https://school.programmers.co.kr/learn/courses/30/lessons/77484)<br><br>

# 문제
<hr>
* 로또를 구매한 민우는 당첨 번호 발표일을 학수고대하고 있었습니다. 하지만, 민우의 동생이 로또에 낙서를 하여, 일부 번호를 알아볼 수 없게 되었습니다. 당첨 번호 발표 후, 민우는 자신이 구매했던 로또로 당첨이 가능했던 최고 순위와 최저 순위를 알아보고 싶어 졌습니다.
* 민우가 구매한 로또 번호를 담은 배열 lottos, 당첨 번호를 담은 배열 win_nums가 매개변수로 주어집니다. 이때, 당첨 가능한 최고 순위와 최저 순위를 차례대로 배열에 담아서 return 하도록 solution 함수를 완성해주세요.
* (문제 전문은 링크에...)<br><br>

# 제한
<hr>
* lottos는 길이 6인 정수 배열입니다.
* lottos의 모든 원소는 0 이상 45 이하인 정수입니다.
    * 0은 알아볼 수 없는 숫자를 의미합니다.
    * 0을 제외한 다른 숫자들은 lottos에 2개 이상 담겨있지 않습니다.
    * lottos의 원소들은 정렬되어 있지 않을 수도 있습니다.
* win_nums은 길이 6인 정수 배열입니다.
* win_nums의 모든 원소는 1 이상 45 이하인 정수입니다.
    * win_nums에는 같은 숫자가 2개 이상 담겨있지 않습니다.
    * win_nums의 원소들은 정렬되어 있지 않을 수도 있습니다.<br><br><br>

# 👀 풀이
<hr>

* 문제를 단순하게 보면 가장 높은 등수를 받을 수 있는 경우는 알아볼 수 없는 숫자 모두가 당첨번호와 일치하는 경우이고, 가장 낮은 등수는 알아볼 수 없는 숫자 모두가 당첨번호와 일치하지 않는 경우이다.
* 그렇기 때문에 민우가 선택한 번호에서 당첨번호와 일치하는 숫자의 개수를 센 다음 0의 개수도 세어서 더해주면 가장 높은 등수이고 더하지 않는다면 가장 낮은 등수이다.
* 2중 반복문을 사용했는데 이 과정에서 내부 반복문의 조건식에 외부 반복문의 변수를 잘못 적어서 인덱스 범위 초과 에러가 났었다... 중첩 반복문을 쓸 때 변수 헷갈리지 않도록 조심하자!

# 코드
<hr>

<script src="https://gist.github.com/miro7923/1a87d96c893a7a2cc45c3468acc77376.js"></script>
