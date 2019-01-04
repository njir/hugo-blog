+++
title = "Swift05 - 메소드"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T17:50:59-06:00"
menu = "main"
+++

#### 메소드


##### 인스턴스 메소드

- 객체를 생성하고 객체에 메소드를 호출하는 방식
- 객체에 반영된 값인 프로퍼티를 사용할 수 있다.

##### 타입 메소드

- 객체를 생성하지 않고 동작하는 메소드
- 메소드를 선언하면서 func 앞에 class 키워드를 이용
- **프로퍼티에 접근할 수 없다**

```swift
class MyClass {
	var property = 0
    class func typeMethod(){
    	property = 3 	// Error. 타입 메소드에서 프로퍼티 접근 불가
    }
    func instanceMethod(){
    	property = 1	// 인스턴스 메소드에서 프로퍼티 접근 가능
    }
}
```

- 인스턴스 메소드는 객체를 생성하고 객체에 호출
- 타입 메소드는 클래스에 호출

```swift
var obj = MyClass()
obj.instanceMethod()

MyClass.typeMethod()
```

- 팩토리 메소드(Factory Method)가 타입 메소드의 대표 예

>Factory Method : 객체를 생성하는 목적으로 작성하는 디자인 패전 중 하나의 코드 작성 패턴

```swift
class Rectangle {
	var width : Int
    var height : Int

    init(width : Int, height : Int){
    	self.width = width
        self.height = height
    }
    
    class func newRectangle(width : Int, height : Int) -> Rectangle{
    	let obj = Rectangle(width: width, height : height)
        return obj
    }
}
```

```swift
//팩토리 메소드를 이용해 객체를 생성하는 코드
var rect = Rectangle.newRectangle(70, height : 80)	// 두번째부터 외부파라미터
```

##### 타입 프로퍼티
- class 키워드를 이용
- 스위프트 버전 1.1에서 클래스에 계산 프로퍼티만 타입 프로퍼티로 사용할 수 있다.

```swift
class Rectangle{
	var width = 0
    var height = 0
    class var name : String {
    	return "사각형"
    }
}
//main.swift
Rectangle.name
```

##### 프로퍼티 감시
- 프로퍼티의 값 변경 감시
- 값 변경 전
  - willSet
  - 변경될 값 : newValue
- 값 변경 이후
  - didSet
  - 이전 값 : oldValue
- Object C - KVO

```swift
class Rectangle{
	var width = 0
    var height : Int = 0 {
    	//프로퍼티에 값이 설정된 후에 동작
        didSet {
        	// 0보다 작으면 0으로 변경
            if height < 0 {
            	height = 0
            }
            println("height didSet \(height) - oldValue : \(oldValue)")
        }
    }
}
```



