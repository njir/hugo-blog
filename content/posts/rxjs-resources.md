+++
title = "RxJS 개발 참고자료"
description = "Udemy 강의에서 소개한 RxJS 개발 참고자료 소개"
tags = [
    "rxjs",
    "Reactive",
    "javascript"
]
date = "2018-02-17"
menu = "main"
+++

# RxJS 개발 참고 자료

## Doc 문서

RxJS 라이브러리의 latest 코드는 [ReactiveX/rxjs](https://github.com/ReactiveX/rxjs)에 올라오고 있다. 이 사이트의 문서를 봐도 되지만, 예제 코드가 많이 없어서 개발할 때 참고하긴 어렵다. <br>
여기 대신에 [Reactive-Extensions/RxJS](https://github.com/Reactive-Extensions/RxJS)의 [api 문서](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api)를 참고하는게 훨씬 낫다.

![reactivex](/images/reactivex.png)

api 문서에 예제 많아 개발하기 좋지만, 문제는 version 4를 기준으로 작성되어 있다. 그래서 이름이 바뀌거나 deprecated된 operator들이 있을 수 있다.
<br> 친절하게도 이 사이트에서 Migration 가이드를 제공해준다. [이 페이지](https://github.com/ReactiveX/rxjs/blob/master/MIGRATION.md)에서 RxJS ver5와 ver4의 달라진 점을 소개해주고 있어, 읽어보기 좋다.
 
- [operators](https://github.com/Reactive-Extensions/RxJS/tree/master/doc/api/core/operators)
- [migration](https://github.com/ReactiveX/rxjs/blob/master/MIGRATION.md) 
 
## RxMarbles

이 사이트는 Rx Observable의 데이터 흐름을 다이어그램으로 표현해주는 사이트다. 모든 operator들이 있는 건 아니지만 주요 operator들은 소개되어 있다.
<br> 다이어그램 상에서 직접 데이터를 움직여볼 수 있어 나같은 초보자가 개념 익히기 좋다.

- [RxMarbles](http://rxmarbles.com/)
- [Github](https://github.com/staltz/rxmarbles)

![rxmables](/images/rxmarbles.png)


## RxVision

RxJS를 테스트해 볼 수 있는 playground를 제공한다. 그리고 RxMarbles과 비슷하나 좀 더 visual하게 observable 스트림을 보여준다. 역시나 개념 익히기 좋은 사이트

![rxvision](/images/rxvision.png)

- [RxVision playground](https://jaredforsyth.com/rxvision/examples/playground/)
- [Github](https://github.com/staltz/rxmarbles)
