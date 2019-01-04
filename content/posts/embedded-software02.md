+++
title = "임베디드 소프트웨어 - IoT 표준 프로토콜 논문"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded",
]
date = "2015-03-09"
menu = "main"
+++

#### Standardized Protocol Stack for the Internet of (Important) Things


이 논문은 적절하게 산업계에서 필요로하는 무선통신 스택을 얘기하려 한다. 논문에서 설명하는 내용을 간단히 소개함.


- *the emergence of the Mobile Internet with the exponential explosion of smart phones*
 - 모바일 인터넷이 스마트폰을 통해 더 많이 확산되고 있다.
- 표준화된 approach가 필요하고, 완성되지 않았지만 큰 그림을 보여줄 수 있다.
 -  802.15.4, 802.15.4e, CoAP, RPL, Standards, IPv6 등 다양한 프로토콜을 아주 간단하게 해서 프로토콜 스택을 통합하여야 한다.
- Kevin Ashton은 RFID로 초기 IoT 터미널을 설명하려 했다.(IoT의 초기 컨셉이 RFID)



*"the Internet of Things is poised to develop and to give rise to important possibilities for developing new services but that it also represents risks in terms of the protection of individual privacy”*

- IoT는 새로운 서비스를 만들 가능성이 많지만 아직 privacy에 대한 컨셉이 아직 없다.(현재까지 없음)

------------------------

*"Although the IoT is a widely used term, its definition is still fuzzy due to the large amount of concepts ~"*

- IoT라는 단어는 널리 쓰이고 있지만, 많은 컨셉들이 계속 overwrapping 되기 때문에 IoT의 정의가 아직 명확하지 않다.

*IoT stands for a “world-wide network of interconnected objects uniquely
addressable, based on standard communication protocols*

- IoT는 addressable할 수 있어야 한다.(찾아갈 수 있어야)

>**address vs identifier**
>
>- address : 주소, 찾는 길이 있다. 찾아가는 길의 address
>- identifier : 식별자 
>  - RFID는 id로부터 출발했다. 실제로 id를 갖고 시스템을 만들었지만 address(주소)는 아니다.
>  - 예를 들어 ISBN으로 책이 무엇인지는 알 수 있지만 이 정보로 책이 정확히 어디있는지는 모른다.
>
>- ex) URL (Locator) + URN (Name) = URI (Identifier)
>  - URL은 address에 속하고 URI는 identifier라고 볼 수 있다. 이는 명확한 차이가 있다.


--------------------------------
*Structurally, the IoT requires software architectures that are able to deal with a large amounts of information, queries, and computation, making use of new data processing paradigms, stream processing, filtering, aggregation and data mining,*

- IoT는 소프트웨어 아키텍처가 필요하다. 
- intelligent한 시스템을 많은 Data가 필요한데, HTTP, TCP 등 다양한 통신 프로토콜을 통해 데이터를 모을 수 있다.
- 이 많은 데이터를 위해 새로운 data processing paradigms(data mining 등)도 필요하게 되지만 결국 가장 기초가 되는 것은 HTTP, TCP, IP 프로토콜이다.
- 하지만 PC에서 사용하는 프로토콜이 아니라 좀 다른 프로토콜이어야 한다.


*In contrast, due to the nature of IoT objects, very low power consumptions are required so any object can plug into the Internet while being powered by batteries or through energy-harvesting*

- 이러한 IP Stack들은 OS에 들어가기 때문에 OS가 중요해진다. 
- very low power consumptions이 필요하기 때문에 리눅스를 사용하기에는 좋지 않다. 
- IoT는 구조(아키텍처)가 중요한데, PC에서 사용하는 OS, Network Architecture들은 크기가 크고 복잡해서 너무 무겁다.
- IoT의 컨셉은 배터리로 5년을 버티는 것이기 때문에 간단하게 만들어야 한다.
>**기존의 HTTP, TCP는 verbose meta-data, header, reliability 측면에서 최적이 아니다.**


---------------------------------------
#### IoT의 세가지 Core Requirements
- A Low Power Communication Stack.
 - 위에서 말했듯이 IoT는 배터리 하나로 몇년을 버텨야 하기 때문에 전력을 많이 소비하지 않는 저전력 스택이 필요하다.
- A Highly Reliable Communication Stack.
 - 통신 프로토콜 또한 reliable 해야한다. 하지만 간단하게 만들어야 하므로 기존 TCP의 Flow Control, Congestion Control 없이 Error Control만 필요하다.
- An Internet-Enabled Communication Stack.
 - 기본적으로 인터넷과 연결되어야 한다.
 
>위 3가지의 필요성을 찾는게 하루이틀에 완성된 것이 아니다.




