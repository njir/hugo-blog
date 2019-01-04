+++
title = "임베디드 소프트웨어 - 6Lowpan Layer"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded",
]
date = "2015-04-16T14:12:59-06:00"
menu = "main"
+++

##### 6lowapn

- 6lowpan layer(= Adapation layer) 기능
 - MTU F&R
     - 1.2K -> 128 bytes
 - Header Compression
     - IPv6 Header : 40 bytes(16src+16dst+8)
     - UDP : 8 bytes

- A(origin)->B->C->D(final dst)

> - A <-> B : NetworkA 
> - B <-> C : NetworkB
> - ***실제 네트워크 주소는 MAC 주소이다.***

>   - 따라서 MAC 헤더는 매번 바뀐다.

 - IPv6 128bits
     - [   64   ][   64   ]
     - 64 : Network ~ Prefix
     - 64 : Host ID <= MAC <- EUI 64
 - IPv4 : A,B,C 클래스 형태 -> CIDR(Classless Inter Domain Router)
     - 현재 우리가 쓰고 있는 형태


- 3가지 경우
- 1) A->B
 - [IP][10bytes]
 - IPv4 : [MAC][IP][DAta]
 - [A->B]이 없음(mac, ip사이에) 32bit가 없어짐
 - [IP] : 8bytes
 - MAC : B<-A
 - IP : 8
 - data : 10
- 2) A->B
 - 100bytes
 - [ip][0 ~    50][51 ~~ 100]
 - fragmentation
     - 각각의 패킷에 ID와 변위(offset)정보가 들어간다.
     - 여기서 ID는 서로 같다
1. [MAC][A->B][id][8byte | ~~~~    ]
 - ID가 같다. offset 0~50
2. [MAC][A->B][id][8 |  ~~   ]
 - ID가 같다. offset 50 ~00
** 여기서도 [A->B] 없어도 된다. 압축 가능
- 3) A->B->C : 여기서는 Header가 있어야 한다.

- 위 패킷에서 8byte 정보를 1bytes로 줄이는거


--------------

**중요 시험문제**

##### IPv6 Neighbor Discovery

> ARP : IP주소 -> MAC주소로 변환
> IPv6에서는 ARP가 존재하지 않음(ARP가 ND와 합쳐짐)

- Host의 전원이 켜졌을 때 
  - 1) RS(Router Solicitation) : Host가 Router가 어딨냐고 물어봄
  - 2) RA(Router Advertisement) : 라우터가 응답함
          - Host는 Netowrk Prefix를 얻어옴(주소)
  - 3) DAD(Duplicate Address Detection)
  - 4) NS(Neighbor Solicitaion) : My address. 네트워크에 내 주소를 뿌림(내 주소를 누가 쓰고있는지 확인하기위해)
       - 주소가 중복된다면 NA(Neighbor Address)를 받음(없어야 정상임)
- **4가지의 ICMP 메세지 : RS, RA, NS, NA**
- Stateless AA


##### DHCP

- 가장 많이 쓰임
- 1) RS
- 2) RA
- 3) DHCP Requeset 라우터가 DHCP Server에 Request
- 4) DHCP Reply : 서버로부터 IP를 받아옴
- DHCP는 **Stateful AA(Stateful Address Auto Configuration)**
    - IPv6 ND는 중복을 각자가 감지하지만 DHCP는 서버가 중복을 처리함

DBM, SNR, Channel Scheduling, Mesh Header, ND 용어 무엇인지

##### 6lowpan ND

- IPv6 ND는 DHCP를 커버하기 어려움
- A -> B -> C -> D(Router)
 - 1)A -> B : RS RA 과정을 거침
  - 2) A -> B : NS
  - 3) B -> Router : Soft Cache (DB에 아이피를 저장하는 형태, 하지만 시간이 지나면 expire)
  - 4) Router -> B : Unicast(IP정보를 B에게 Unicast로 전달)
  - 5) B->A : NA
 


