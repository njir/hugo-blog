+++
title = "임베디드 소프트웨어 - IEEE 802.15.4, 15.4e"
description = "아주대학교 임베디드 소프트웨어 수업 정리"
tags = [
    "embedded",
]
date = "2015-03-17"
menu = "main"
+++

#### 프로토콜 버전
- 버전 : 15.4 
  - 2003 ~ 2006년도
- 버전 : **15.4e**  (4e : Industry IoT, 산업용 IoT)
  - ~ 2010 
 
>**Industry IoT의 특징:**
>
>- 신뢰성
>  - 예전부터 무선통신은 신뢰성과는 관련이 없었지만, 신뢰성이 있다고 얘기함(but 잘 안믿음. banking, VPN처럼)
>  - ex) Internet <-> Intranet
>  - 방향 : battery -> "Wifi" -> **속도를 높이는게 목표**
>- 실시간성
>- Industry IoT의 표준화 : IEC, ISO (국제 표준이 완성됨)
>- IoT의 표준화 목표 : IETF(인터넷 표준 만드는 곳) -> GP IoT
					
이 논문에서는 산업용 IoT에 대한 얘기를 많이함


#### IEEE 802.15.4
- Low-Power Radio Hardware
 - 무선으로 radio가 나감(transmission over the air)
 - 데이터를 아날로그 시그널로 바꿔줘야함
 - PA에서 신호 증폭해서 송신
 - sensitivity : 리시버가 받을 수 있는 가장 약한 신호, 
     - 1pW까지 받을 수 있음  
 - low power radio의 output : 0 dBm or 1mW
 - -90 dBm : 1pW
 - 단위 Conversion 중요

>**decibel과 dBm의 차이**
>
>- decibel은 bel의 1/10
>- dB : 어떤 측정값을 log스케일로 표현
>- dBm : mW단위의 전력을 dB스케일로 나타낸 단위
>  - dBm은 절대값 
>- 1W : 0dB(10*log1 = 0dB)
>   - 1mW = -30dB


- Fading, Shadowing 때문에 시그널이 약해짐
  
>**Fading**
>
>- Fading : 경로가 다른 2 이상의 전파가 간섭(상보, 소멸)하여 신호가 불규칙하게 변하는 현상
>   - ex) AP에서 스마트폰이 신호를 받을 때 여러개가 보내지는데 각각의 신호가 동시에 도착하지 않는다. 이 신호들이 서로 간섭하여 신호가 일그러짐 
>- 이를 막기 위해 **다이버시티** 사용

- dBm 높다는 것은 약한신호도 증폭할 수 있다.
- PA & LNA : 상당량의 전류 소모
 - ex) AT86RF231이라는 칩을 사용하면 13mA 전류가 흐른다.
- 결론 : 보내는 거와 받는 과정이 모두 파워 소모를 한다.
  - 이 파워가 모두 배터리다.
  - ex) AA 배터리가 3000mAh일 때, 13mA 소모하는 칩을 사용하면 230시간동안, 10일동안 사용 가능(3000mAh / 13mA = 230h = 약 10일)
     - mAh : charge의 단위, coulomb : 1A가 1초동안 흐른 전하량
     





#### IEEE 802.15.4e
- IEEE 802.15.4 PHY layer는 오프셋을 사용하는 QPSK 방식(**O-QPSK**) 사용
- 250 kbps로 굉장히 낮음
- 8 more chips are sent over a 2 Mcps link. -> Direct Sequence Spread Spectrum(DSSS)
- 128 bytes 의 기본 슬롯 싸이클(아래 그림 참조)
![](/images/IEEE1.png)
    - 그림(b)의 time slot이 그림(a)의 각 0,1,2,..에 들어간다.

- 채널 하핑 : 보내거나 받을게 있을때만 깨어남 나머지는 잔다.(아래 그림 참조)
![](/images/IEEE2.png)
    - 위의 time slot 0에서는 A, B를 제외하곤 다른들은 다 잔다.
    - time slot 1에서는 DCA 세개만 깨어있음(둘이 동시에 보내면 깨지기 때문에 캐리어 센싱)
    - time slot 3은 BFEGD가 깨어있으나 주파수가 다르기 때문에 싱크를 계속 맞춰야한다. 즉, 100ms 마다 비콘을 맞추고 있는것(beaconing과는 다름)

- 15.4e : 채널 하핑(Fig1,2, 그림) + **Beacon(동시에 깨어나고 자는 방식)** 
>- Beaconing : 동시에 자고 동시에 일어나는 방식
>- Hopping은 TSMP의 방식

- 위의 두 방식을 통해 1%를 낮출 수 있음

>**TSMP**
>
>- 표준 프로토콜이 아님
>- 산업용으로는 많이 사용
>- ex) Emerson : 공장에 9200개의 산업용 네트워크 설치, steel, refineries, chemical, industrial ....
>- GP IoT



