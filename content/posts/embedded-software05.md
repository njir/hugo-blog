+++
title = "임베디드 소프트웨어 - Connecting to the Internet 01"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded",
]
date = "2015-04-07T16:12:59-06:00"
menu = "main"
+++


##### IEEE802.15.4e가 wifi에 비해 에너지 소모를 줄일 수 있는 방법(Power Plug : Main Power, Battery On : No Replace)

- TSCH(Time Slicing이 배터리, Channel Hopping이 패딩(jamming) 방지)
 - time slicing, 계속 잘라서 쓰자
 - 필요할 때만 온전히 다쓴다.
 - 최소단위로 쪼개서 쓰는 것
- Beaconning
 - 비콘이 뜰 때 Cyclic Behavior(주기적으로 깨어남)
 
>**Notes:** 
> 둘다 표준이지만 모두 다 구현할 필요 없다. 대세는 TSCH방식 
> 

Frequency Hopping은 Jamming을 방지하기 위해(배터리와는 무관)


##### 2. B-> A <- C <- D

![](/images/abce.png)


- cycle 0일 때
아무것도 안붙어있으면 0번만 듣고 있음
- ex) Offset = 0, slot = 0 : A가 Advertisement (A->ALL)
 - B와 C가 Join request 해야함(***Join Request는 동시에 할 수 없음***)
 - A가 B,C에게 Join Response
 - A가 C에게 Set Link(off=0, t=2, Adv / off=1, t=2, RX모드), SetLink(... C->A)
 - C가 Adv
 - D가 C에게 Join Request
 - C가 A에게 Join Request
 - A가 C에게 Join Response
 - C가 D에게 Join Response
 - 그 후 A가 Set Link
 - D->C, C->A time slot 순으로 가는게 효율적이다.(ex time slot=5일때 D->C, slot=6일때 C->)
 - D도 브로드캐스트하게 해줘야함(Router(동그라미)는 브로드캐스트, Host(사각형)는 브로드캐스트 필요 없음)

> - 3장은 Channel & Mac(Medium Access Control)에 관한 얘기
> - 여기서 Medium은 Air -> 시간을 쪼개서 전송한다는 뜻

##### 4장 Connecting to the internet

- Low Power WPAN
 - 작은 패킷 사이즈
 - low bandwidth
 - **high unreliabilty**
 - low cost
 - long idle periods
 - mesh topologies
 - battery
 - support for address with different lengths
- IPv6의 MTU(Maximum Transmission Unit)
 - 40 bytes long IPv6 header
 - IPv6 default minimum MTU size = 1280 bytes
 - 한번에 패킷을 보낼 수 있는 최대 크기
 - MTU 자체가 1280bytes = 1.2kb보다 커야한다.(권장사항은 1.5kb)
 - 패킷 보낼 수 있는 사이즈가 (1byte ~ 1280bytes)
 - **min MTU = 1.2k**
 - MTU가 커지면 커질수록 한번에 보낼 수 있는 패킷크기가 커진다. -> 에러가 작아진다
 - MTU 작아지면 에러율 커짐
- IoT(IEEE802.15.4)에서는 MTU size = 128 bytes이다.
 - IoT를 IPv6에 어떻게 달수 있나
 - 눈속임(6LoWPAN Adaptaion Layer을 통해서)
 
##### A(Server)-> B-> C(IoT 6LoWPAN)-> D-> E(Sensor)

> - A : Originator, E : Final Destination
> - A에서 B MTU = 10K
> - B에서 C MTU = 3k
> - C에서 D MTU = 128bytes
> - D에서 C MTU = 128bytes
>
 
1. A가 B에게 전송할 때, Ping으로 테스트함(물어봄)
 - MTU보다 크면 ICMP Error(Packet Too Big), 자신의 MTU정보(B의 MTU = 3K) 전달
 - 위 과정을 PATH MTU discovery
 - B가 자신은 MTU 3k라고 보내고, A는 3K의 패킷을 보냄
2. B가 C에게 전송하는데, 보낼 패킷을 Ping으로 테스트
 - **C는 ICMP Error(PTB) 보내는데, 여기서 MTU정보는 1.2K로 보냄(속여서, min MTU)**
 - B는 1.2K의 패킷을 전송함
3. C는 1.2K패킷을 받고, 6LoWPAN가 패킷을 10개로 쪼개야한다.