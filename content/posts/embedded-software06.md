+++
title = "임베디드 소프트웨어 - Connecting to the Internet 02"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded",
]
date = "2015-04-07T17:12:59-06:00"
menu = "main"
+++

##### Path MTU Discovery

- Path MTU는 전체 구간의 **최소값**임
- IPv6는 중간에 짜르는게 안됨(Fragmentation X)
- IPv6 : 128bit = 16byte(주소) -> 40byte(16byte SRC + 16byte DST + 8byte)

>***why?***
> - Packet header가 4배나 늘어남 -> 속도 느려짐
>- 라우터의 유연한 기능은 버리자
>


- IPv6는 전송을 못하면 ICMP Error를 전송(B->A)
  - A -> B -> **(IoT구간 MTU=128byte) C -> D**
     - A: MTU=100k
     - B: MTU=50k
     - C: MTU=128byte(실제로)
  - ICMP Error(PTM)
  - B->C로 보낼 때 **C는 MTU를 1.2K라고 보냄(프로토콜 상)**
  - 결국 C는 1.2K 패킷을 받게 됨
  - 이 패킷을 1/10로 나눠야함 -> 6LoWPAN


##### 6LoWPAN = Adaptation Layer(2.5계층, Shim Layer)

- Error가 크고, 속도가 느리고, **Low Power Consumption**(***원인***)-> MTU가 128byte로 작아짐(***결과***)

>**Adaptation Layer가 필요한 이유:**

- **Fragmentation & Reassemble**(필수)
- Header Compression
     - IPv6의 헤더 크기를 줄임(꼭 안해도 됨)

![](/images/packet.png)

 - FCF부터 FChk까지가 127bytes
 - Len(1bytes)까지 포함해서 128bytes
 - SFD : 패킷 관련 내용 시작
 - Dst16, Src16 : 맥주소 (Dst가 중요함. 내가 듣고 있다가 DST가 ‘나’이면 그 이후부터 들으면 됨)
  - 그 이후부터 데이터임
  > **Mac주소의 사이즈** : EUI 48, 64bit(대세는 64bit가 되어감)
  >


##### Router Over IP
![Router Over IP](/images/routeoverip.png)

- IP계층의 헤더를 보고 라우팅함.
- 중간 라우터 오버헤드 높음
- 속도 느림(중간에 매번 합쳐지고 다시 쪼개고 그러니까)
- 메모리 많이 있어야함
- 미국의 IETF의 표준
- ex) 첫번째 그림에서 파란색 부분을 쪼개야 할 때(A->B)
 - 1) B<-A [  A->D | Data1  ] 
 - 2) B<-A [ ----  Data2 ----    ] 
 - 3) B<-A [ ----  Data3 ----    ]
     - 2), 3)은 어디로 보낼지 헤더 정보가 없다.
     - 해결방법 : 쪼개진 패킷은 각각의 ID가 있는데, 중간 라우터마다 다시 합치고 쪼갠다(Fragmentation & Reassemble)
     
     
##### Mesh Under IP

- 중간 라우터에서 합쳐지지 않고 따로따로 감 
- 도착지에서 합쳐지기 때문에 중간에 유실되면 확인방법 없음
- 속도 빠름, 중간 라우터에서 메모리 필요 없음
- 유럽의 ISO, IEC 는 Mesh Under로 표준
- ex) A->B로 보낼 때 쪼개긴 하지만 A->D 정보를 쪼개진 패킷의 Adaptation Layer에 각각 넣음
 - [A->D][Data1]
 - [A->D][Data2]
 - [A->D][Data3]
     - 경로 정보가 각각의 패킷에 담겨있다.
     - **앞으로 뺀 헤더([A->B])를 Mesh Header =  MultiHop Header 라 부른다.**
     - 각각의 독립적인 패킷이 되버림


