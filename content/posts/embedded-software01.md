+++
title = "임베디드 소프트웨어 - IoT 소개"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded"
]
date = "2015-03-08"
menu = "main"
+++

#### Introduction to IoT Platform

IoT의 개괄적인 구조는 다음 그림과 같다.
![](/images/iot_tak.png)

- Platform은 IoT이 돌아가는 플랫폼으로 예를 들어 WAS, open-iot 등이 있다.
- IoT에서 중요한 부분은 Machine To Machine Network이다. 이는 우리가 이미 알고 있는 LTE 혹은 Wifi로 사물끼리 무선으로 연결된 망이다. 
- 곧 각각의 집이나 건물들이 이 네트워크에서 무선으로 연결된 M2M Gateway의 역할을 하게 될 것이고, 이 M2M Gateway가 집에 있는 또 다른 device(M2M Host)들을 무선 연결한다.
- M2M Host들은  집, 자동차 등 여러 모든 사물이 될 수 있고, M2M Gateway와 연결 혹은 M2M Network에 직접 연결할 수 있다.


#### IoT 프로토콜
- IoT도 인터넷이기 때문에 protocol이 필요하다. -> Layer 2계층의 프로토콜
- 위 그림에서 M2M Host들간의 통신은 Layer2의 프로토콜, M2M Network와 M2M Host의 통신에 쓰이는 프로토콜은 L3부터 상위 계층의 프로토콜을 사용한다.
- M2M Host는 Sensing Data 같이 간단한 데이터(온도가 몇인지, 시동이 켜졌는지 여부 등)를 주고 받는 일종의 작은 서버가 된다.
- HTTP, TCP, IP는 가장 중요한 Protocol이지만 무겁다.
- 사물들(Things=M2M Host)은 8bit, 16bit처럼 작은 칩이 들어가기 때문에 경량화된 프로토콜이 필요하다. 

#### IoT 방향
- IoT의 처음 출발은 RFID부터 시작한다.
- RFID는 바코드를 대체하기 위해 나왔지만 비싸서 바코드 대체는 실패함
- 그 후 NFC를 거쳐 Bluetooth, FinTech까지 개발되었다.

> **Bluetooth:**
>  B3.0 --> B4.0 --> BLE-Beacon --> BT Mesh WorkingGroup까지 블루투스도 Mesh 네트워크로 진화 중

- 하지만 이들은 단지 무선 신호 일뿐 ***기기당 IP를 쓸 필요가 있느냐***라는 점에서 모두 IoT가 아니라고 볼 수 있다. 
- IoT는 **"Mesh"** Network of Things로 사물간 그물처럼 연결되어 있어야 한다.
- Star형 구조처럼 AP에 여러 PC가 연결되어 있는게 아니라 Mesh구조는 AP와 PC뿐만 아니라 PC와 PC까지 연결되어 그물처럼 망을 이룬다.

#### M2M/IoT Application 종류
- Assert Tracking : 택배 도착 알림
- Advanced Metering(Smart Grid) : 효율적 사용을 위해 전력 산업 재편 
- ex) 계량기의 변화 -->스마트 미터(Bluetooth 등)
- Building / Home Automation
- Industrial Automation

> 주거환경, 산업 전반에 걸친 IoT 적용

#### M2M/IoT Network Topologies for ZigBee
![](/images/topology.gif)

> **Tips:**
>
>- Mesh 구조는 싸이클이 하나 이상 존재한다.
>- 이를 위해 라우팅 기술이 필요한데, OSPF, RIP 같은 라우팅 프로토콜은 8bit칩에는 무겁다.


**OSI 7 Layer에서 IoT만을 위한 프로토콜을 정의하고 배우는 것이 이 강의의 목표이다.**