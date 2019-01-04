+++
title = "Swift12 - 옵셔널 체인"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T22:02:59-06:00"
menu = "main"
+++

##### 옵셔널 체인

- 옵셔널로 선언된 변수나 프로퍼티에 접근하려면 if let을 이용한 옵셔널 바인딩을 이용하거나 강제 어래핑을 한다.
- 옵셔널 채인은 nil로 인한 에러 없이 옵셔널 타입 사용 가능
- 옵셔널 프로퍼티에 ?를 이용해서 접근하는 방식

```swift
let number3 = john.phone?.number
```

##### 타입 체크와 변환

- 타입 체크 : is
- 타입 변환 : as

```swift
let pets : [Animal] = [Dog("스누피"), Cat("이엘"), Cat("라즈")]
pets[0] is Cat
pets[1] is Cat
//
let myDog : Dog = pets[0] as Dog
```

- 타입 체크가 필요한 상황
 - 상속
 - AnyObject : 모든 오브젝트
 - Any : 모든 타입
      - 타입별 메소드, 프로퍼티 사용 불가 -> 타입 캐스팅 필요
      
```swift
for one in anyArray{
	if one is String{
    	println("문자열 \(one)")
    }
    else if one is Int {
    	var i = one as Int
        println("다음 숫자 \(i+1)")
    }
}
```

 
##### 타입 알리아스

- 타입에 이름주기

```swift
 typealias Century = Int
 var thisEra : Century
 thisEra = 21
```
 
- 클로저는 타입 알리아스

```swift
typealias ResultHandler = (Int)->Void
``` 


##### 제네릭

- 타입에 대한 엄격함
- 타입별로 같은 기능
- 제네릭 프로그래밍 
 - 데이터의 타입에 의존하지 않고 작성하는 프로그래밍 방식
- 두 종류의 타입을 사용하는 예 : 딕셔너리

```swift
 struct Array<T> {
 	subscript (index : Int) -> T
    
    var first : T? { get}
    var last : T? { get }
    mutating func append(newElement : T)
}
```
 
##### 접근 조절

 - 클래스 기본 값 : internal
 - 프로퍼티, 메소드 기본값
  - public 클래스 : internal 프로퍼티, 메소드
  - internal 클래스 : internal 프로퍼티, 메소드
  - private 클래스 : private 프로퍼티, 메소드
 - 클래스의 제어 레벨을 넘지 못함
 
```swift
 public class PublicClass{
 	public func publicMethod(){
    	println("public")
    }
    internal func internalMethod(){
    	println("internal")
    }
    private func privateMethod(){
    	println("private")
    }
}
```

 
 
 
 
 
 
 
 
 
 

