+++
title = "Swift04 - 클래스"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T17:35:59-06:00"
menu = "main"
+++

#### 클래스
- 프로젝트에서 클래스는
 - 하나의 swift 파일에 다수의 클래스 작성
 - 클래스마다 swift 파일 작성
- 템플릿
 - Cocoa Class(클래스를 만듦, 부모클래스 정할 수 있음)
 - Swift File(부모 클래스 없이 파일만)
- main.swift : 처음 동작 코드
- 객체 생성

```swift
var obj = Rectangle()  // new 사용 안함 
let obj2 = Rectangle()
```

> - var 새로운 객체 대입 가능
> - let : 대입 불가능

- 객체 생성 - 주소
- 객체 비교 : 같은 주소 vs 같은 데이터
- 객체 주소 비교 : '==='

```swift
var obj1 = Rectangle()
var obj2 = obj1
var obj3 = Rectangle()

obj1 === obj2 //true
obj1 === obj3 //false
```

##### 프로퍼티 : 클래스나 구조체 값 다루기

- 종류 : 저장 프로퍼티, 계산 프로퍼티
- 저장 프로퍼티(stored property)
 - 값을 저장하고 얻어오는 용도
 - 클래스 내 변수, 상수 정의
      - 초기값, 옵셔널 
- 계산 프로퍼티(compucated property)
 - 값 저장하지 않음
 - 계산된 값을 얻어오거나, 계산하도록 제공
 - 메소드가 아님
 - 읽고 쓰기 가능 : get/set 모두 작성
 - 읽기 전용: get만 작성, get 선언 생략가능
 - 사용 방법
 
```
var [프로퍼티 이름] : [type] {
	get {
    	return [계산 결과]
    }
    set(newValue){
    	}
    }
```   

##### 메소드 : 클래스 내에 작성하는 함수

- 첫번째 파라메터 : 외부 파라미터 이름 사용 안함
- 두번째 파라메터부터 : 외부 파타미터 이름 사용
- 내부 파라미터 이름으로 자동 사용

- 외부파라미터 이름 설정
- 두번째 파라미터 외부 파라미터 이름은 내부 파라미터 이름 그대로
- 첫번째 파라미터도 외부 파라미터로 설정하려면 # 주거나, 수동으로 지정

>**함수와 클래스함수의 차이점:**
> - 클래스 함수는 두번째 파라미터부터 외부 파라미터 이름 사용해야함

- 외부 파라미터 이름을 사용하지 않으려면 _(언더스코어) 기호를 붙인다. -> 두번째 파라미터부터 적용가능

```swift
// counter.swift
class Counter{
    var count = 0
    func increment(){
        count++
    }
    func incrementBy(amount : Int, numberOfTimes : Int){
        count += amount * numberOfTimes
    }
    func reset(){
        count = 0
    }
    func test(amount : Int, time number : Int){
        count += amount * number
    }
    func notUsingExternal(amount : Int, _ number : Int){
        count += amount * number
    }
    func selfCount(count : Int){
        self.count = count
    }
}

// main.swift
let counter = Counter()
counter.increment()         // 1
counter.incrementBy(5, numberOfTimes : 5)   // 6
counter.reset()                  // 0
counter.test(4, time : 6)
counter.notUsingExternal(3, 5)

```

##### 초기화

- 클래스 초기화
- 모든 프로퍼티(옵셔널 제외)는 객체 생성시 초기화
 - 초기값
 - 초기화 메소드
- 객체 준비 과정
 - 메모리 공간 준비
 - 객체 사용 준비
   - 초기값이 설정된 프로퍼티 - 초기값 설정
   - 옵셔널의 초기값 - nil
   - 그외 초기화 과정 필요
   
##### 초기화 메소드 init()

- 클래스의 프로퍼티가 옵셔널이 아니고 초기값ㅇ르 설정하지 않으면, 객체를 생성하면서 이 프로퍼티 초기값을 설정하는 메소드가 필요 -> Initializer
- 초기화 메소드는 func 지시자가 없고 반환값이 없는 init 메소드

```swift
class Rectangle(){
	var width : Int
    var height : Int
    let edges = 0
    // 초기화
    init(){
    	width = 0
        height = 0
        edged = 4 	// 초기화메소드에서 상수값을 설정할 수 있다.
    }
}
```

>**tips:**
>
> init 메소드는 다른 메소드와는 다르게 첫 번째 파라미터부터 외부 파라미터 이름을 사용한다. 외부 파라미터를 사용하지 않으려면 _(언더스코어) 기호 사용하거나 수동으로 지정하여야 한다.
>

##### Designated Initializer : 모든 값 초기화

- 클래스에 초기화가 안된 프로퍼티가 있다면 반드시 1개 이상의 Designated Initializer가 있어야 한다.
- 초기화가 필요한 **모든 프로퍼티**의 초기값을 설정

```swift
class MyClass {
	var a : Int
    var b : Int  
    func otherprepare() {
    	//다른 초기화 동작
    }
    init(a : Int, b : Int){
    	self.a = a
        self.b = b
        otherprepare()	// 프로퍼티 초기화 후에는 허용
    }
}
```
```swift
// 단, 초기화 과정이 끝나기 전에 다른 메소드 호출은 에러 발생
init(a : Int, b : Int){
  	self.a = a
    otherprepare()	// Error
    self.b = b
}
```

##### Convenience Initializer

- 초기화가 필요한 모든 프로퍼티의 초기값을 설정하지 않는다.
- 단독으로 객체의 초기화 과정 완료 불가
- 다른 초기화 메소드를 이용해서 초기화 과정 수행

>Initialize Delegation : 다른 초기화 메소드에게 나모지 초기화를 위임하는 것

```swift
// Music.swift
class Music {
    var title : String
    var artist : String
    
    init(title : String, artist : String){
        self.title = title
        self.artist = artist
    }
    convenience init(artist : String){
        self.init(title : "", artist : artist)
    }
}

// main.swift
let music3 = Music(artist: "다이나믹듀오")
music3.title = "고백"
```
