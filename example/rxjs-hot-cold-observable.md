+++
title = "Hot Observable vs Cold Observable"
description = "Hot Observable, Cold Observable의 차이점 정리"
tags = [
    "rxjs",
    "Reactive",
    "javascript"
]
date = "2018-03-12"
menu = "main"
+++

https://alligator.io/rxjs/hot-cold-observables/

Observable은 데이터가 어디서 생성되는지를 기준으로 ```hot``` 또는 ```cold```으로 나뉜다.

# Cold Observable
**Cole Observable**은 그 Observable 자체에서 data producer가 생성되는 observable이다. 
