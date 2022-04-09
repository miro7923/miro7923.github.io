---
title: 컴퓨터 네트워크) Transmission Control Protocol
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Computer Network
tags:
    - CS
    - Network
    - Transport
---
# 👀 Transmission Control Protocol
* `TCP`에서 데이터 송수신 시 연결과 흐름 제어 동작을 수행하기 위해 사용되는 프로토콜
* `UDP`에 비해 긴 헤더로 구성되어 있으며 `Sequence number`와 `Acknowledgment number`를 이용해 데이터가 전송되었는지 확인하고 흐름을 제어한다.

## Sequence number
* 전송된 세그먼트의 순서로 전체 메세지에서 어느 부분에 해당하는지 구분하는 번호
* 1, 2, 3, ... 순서로 세그먼트가 전송된다고 하면 `Sequence number`를 통해 지금 받고 있는 부분이 어디쯤인지 알 수 있다.

## Acknowledgment number
* `receiver`가 다음에 받아야 하는 `Sequence number`
* `receiver`가 세그먼트를 받고 나서 다음에 몇 번 세그먼트를 받아야 하는지 `sender`에게 알려줄 때 사용하는 번호
* `sender`는 `receiver`로부터 온 `Acknowledgment number`를 보고 어떤 세그먼트를 보내야 하는지 알 수 있다.

* `TCP` 프로토콜에서 데이터의 송수신은 `sequence number`와 `ACK`의 확인으로 이루어지며 중간에 데이터가 누락되었다면 `ACK`를 통해 몇 번이 누락되었는지 확인하고 재전송 할 수 있다.

## flow control
* `receiver`가 수용할 수 있는 속도보다 훨씬 빠른 속도로 보내는 것을 막기 위해 사용된다.
* 데이터를 보낼 때 데이터를 받을 버퍼의 크기를 미리 제한한 뒤 그만큼씩 보내는 것이다.<br><br>

# Congestion Control
* 트래픽의 양이 늘어나면 전송 가능한 양이 점점 줄어들어서 나중에는 하나도 보내지 못 하게 된다. 
* 그래서 지속 가능한 서비스를 할 수 있도록 네트워크 상에서 전송되는 데이터의 총량을 제한하는 것이다.
* `end-to-end`와 `network-assisted` 방식이 있는데 `end-to-end`의 대표적인 방식은 `TCP`가 있고 `network-assisted`에는 `ATM`이 있다.

## AIMD
* 혼잡이 생기면 `congestion window`의 크기를 갑자기 줄여서 혼잡을 해결한 후에 다시 크기를 서서히 증가시키는 것

## Congestion Window(cwnd)
* 네트워크가 `congestion`을 일으키지 않는 한도 내에서 `ACK`를 받지 않고 보낼 수 있는 데이터의 양
* `cwnd`의 크기는 네트워크 상황에 따라서 유동적으로 조절된다.<br><br>

# 한 번 더 비교해보는 TCP vs UDP
## TCP
* 데이터를 주고받을 당사자 간에 연결을 먼저 수립한 다음에 데이터의 송수신을 시작하고 데이터가 정확하게 도착하게 하기 위해서 흐름 제어 동작을 수행하기 때문에 `UDP`에 비해서는 느리다. 그만큼 데이터가 중간에 누락되지 않고 정확하게 도착하는 것을 보장할 수 있다.
* 데이터의 정확한 송수신을 요구하는 `Email`, `Web browsing`에서 주로 사용된다.

## UDP
* 연결과 흐름 제어를 위한 동작을 수행하지 않고 받은 대로 그냥 보내기 때문에 `TCP`에 비해서는 빠른 속도를 보여준다. 그만큼 데이터가 정확하게 도착한다는 보장이 없다.
* 중간에 데이터가 좀 누락되어도 괜찮고 속도가 빠른 것이 더 중시되는 실시간 스트리밍 서비스에서 주로 사용된다.<br><br><br>

# 출처
* [컴퓨터 네트워킹 - 부산대학교 K-MOOC 공개강의](http://www.kmooc.kr/courses/course-v1:PNUk+CN_C01+2021_KM_013/video)
