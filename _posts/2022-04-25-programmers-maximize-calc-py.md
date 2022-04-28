---
title: Python) 프로그래머스. 수식 최대화
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Programmers
tags:
    - Algorithm
    - Programmers
    - Stack
    - Python
---

# 문제 링크
<hr>
* [https://programmers.co.kr/learn/courses/30/lessons/67257](https://programmers.co.kr/learn/courses/30/lessons/67257)<br><br>

# 문제
<hr>
* IT 벤처 회사를 운영하고 있는 라이언은 매년 사내 해커톤 대회를 개최하여 우승자에게 상금을 지급하고 있습니다.
* 이번 대회에서는 우승자에게 지급되는 상금을 이전 대회와는 다르게 다음과 같은 방식으로 결정하려고 합니다.
* 해커톤 대회에 참가하는 모든 참가자들에게는 숫자들과 3가지의 연산문자(+, -, *) 만으로 이루어진 연산 수식이 전달되며, 참가자의 미션은 전달받은 수식에 포함된 연산자의 우선순위를 자유롭게 재정의하여 만들 수 있는 가장 큰 숫자를 제출하는 것입니다.
* 단, 연산자의 우선순위를 새로 정의할 때, 같은 순위의 연산자는 없어야 합니다. 즉, + > - > * 또는 - > * > + 등과 같이 연산자 우선순위를 정의할 수 있으나 +,* > - 또는 * > +,-처럼 2개 이상의 연산자가 동일한 순위를 가지도록 연산자 우선순위를 정의할 수는 없습니다. 수식에 포함된 연산자가 2개라면 정의할 수 있는 연산자 우선순위 조합은 2! = 2가지이며, 연산자가 3개라면 3! = 6가지 조합이 가능합니다.
* 만약 계산된 결과가 음수라면 해당 숫자의 절댓값으로 변환하여 제출하며 제출한 숫자가 가장 큰 참가자를 우승자로 선정하며, 우승자가 제출한 숫자를 우승상금으로 지급하게 됩니다.

* 예를 들어, 참가자 중 네오가 아래와 같은 수식을 전달받았다고 가정합니다.

* "100-200*300-500+20"

* 일반적으로 수학 및 전산학에서 약속된 연산자 우선순위에 따르면 더하기와 빼기는 서로 동등하며 곱하기는 더하기, 빼기에 비해 우선순위가 높아 * > +,- 로 우선순위가 정의되어 있습니다.
* 대회 규칙에 따라 + > - > * 또는 - > * > + 등과 같이 연산자 우선순위를 정의할 수 있으나 +,* > - 또는 * > +,- 처럼 2개 이상의 연산자가 동일한 순위를 가지도록 연산자 우선순위를 정의할 수는 없습니다.
* 수식에 연산자가 3개 주어졌으므로 가능한 연산자 우선순위 조합은 3! = 6가지이며, 그 중 + > - > * 로 연산자 우선순위를 정한다면 결괏값은 22,000원이 됩니다.
* 반면에 * > + > - 로 연산자 우선순위를 정한다면 수식의 결괏값은 -60,420 이지만, 규칙에 따라 우승 시 상금은 절댓값인 60,420원이 됩니다.

* 참가자에게 주어진 연산 수식이 담긴 문자열 expression이 매개변수로 주어질 때, 우승 시 받을 수 있는 가장 큰 상금 금액을 return 하도록 solution 함수를 완성해주세요.<br><br>

# 제한
<hr>
* expression은 길이가 3 이상 100 이하인 문자열입니다.
* expression은 공백문자, 괄호문자 없이 오로지 숫자와 3가지의 연산자(+, -, *) 만으로 이루어진 올바른 중위표기법(연산의 두 대상 사이에 연산기호를 사용하는 방식)으로 표현된 연산식입니다. 잘못된 연산식은 입력으로 주어지지 않습니다.
* 즉, "402+-561*"처럼 잘못된 수식은 올바른 중위표기법이 아니므로 주어지지 않습니다.
* expression의 피연산자(operand)는 0 이상 999 이하의 숫자입니다.
* 즉, "100-2145*458+12"처럼 999를 초과하는 피연산자가 포함된 수식은 입력으로 주어지지 않습니다.
* "-56+100"처럼 피연산자가 음수인 수식도 입력으로 주어지지 않습니다.
* expression은 적어도 1개 이상의 연산자를 포함하고 있습니다.
* 연산자 우선순위를 어떻게 적용하더라도, expression의 중간 계산값과 최종 결괏값은 절댓값이 263 - 1 이하가 되도록 입력이 주어집니다.
* 같은 연산자끼리는 앞에 있는 것의 우선순위가 더 높습니다.<br><br><br>

# 👀 풀이
<hr>
* 아이디어가 떠오르지 않아서 질문 게시판을 참고했다.
1. 숫자와 연산자를 각각 분리한다.
1-1. 숫자 분리시 `replace()` 메서드를 사용해 연산자들을 동일한 문자로 바꾼 뒤 최종적으로 `split()`을 사용해 분리한 리스트를 저장한다.
1-2. 연산자 분리시 `expression` 문자열의 원소들을 순회하며 `isdecimal()` 메서드로 숫자가 아닌 것만 리스트에 저장하면 된다.

2. 연산자가 3개 뿐이기 때문에 총 6개의 경우의 수가 나온다. 6가지 경우의 조합을 만들어서 각 경우에 따른 우선순위대로 계산을 시작하면 되는데 나는 `itertools`의 순열 메서드로 순열을 만들어 사용했다. 최대 경우의 수가 6이라서 하나씩 손으로 써서 사용한 사람도 많았다.

3. 2에서 구한 각 우선순위 케이스를 이용해 계산을 시작하면 되는데 이 때 스택을 사용한다. 숫자 스택과 연산자 스택 두 개를 만든 다음 1에서 구한 숫자와 연산자 리스트에서 숫자와 연산자를 하나씩 뽑아서 각각 스택에 저장한다. 방금 저장한 연산자가 현재 우선적으로 사용해야 하는 연산자라면 스택에서 꺼내고 연산에 사용할 숫자 2개도 스택에서 꺼내서 연산한 다음 숫자 스택에 다시 넣는다. 이것을 1에서 구한 연산자 분리 리스트의 길이만큼 반복한다.

4. 3에서 구한 결과값의 절대값을 정답에 저장된 값과 대조하여 최대값을 찾는다. 2에서 구한 케이스가 모두 끝날 때까지 반복한다.<br><br>
 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/c4f27f01afbaf0beb610650ab9f35825.js"></script>