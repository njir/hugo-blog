+++
title = "Swift11 - Extension, 연산자, 비교"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T21:58:59-06:00"
menu = "main"
+++


##### 확장

- 첨자 표기
- 타입 내 타입
- 연산자 정의
- 리터럴 호환
- 클래스(구조체, enum) 확장
 - 프로퍼티, 메소드, 프로토콜, 서브스크립트, nested type
- ObjectiveC의 카테고리(Category)
- 계산 프로퍼티만 가능(저장 X)
- 프로토콜 채택
- Subscript 작성 가능

```swift
extension Dog {
	func bike() {
    	println("dfdfdf")
    }
}
```

##### Subscript

- subscript: 첨자 표기[n]
 - 인덱스(숫자), 키, 범위
 
```swift
extension String {
	subscript(index: Int) -> String {
    	return self[index..<index]
    }
    subscript(range:Range<Int>) -> String {
    	let start = advance(startIndex, range.startIndex, endIndex)
        let end = advance(startIndex, range.endIndex, endIndex)
        return self[start...end]
    }
}
```


##### 타입 내 타입

- 클래스나 구조체, Enum 타입 내부에 다른 타입 작성 가능

##### 싱글턴 패턴

- 어떤 클래스의 인스턴스는 오직 하나임을 보장하며, 이 인스턴스에 접근할 수 있는 전역적인 접촉점을 제공하는 패턴
- 다른 객체가 아니라 같은 객체를 생성하도록

```swift
class MyClass {
    class var sharedInstance: MyClass {
        // 클래스에 타입 계산 프로퍼티(class stored property)지원이 안돼서 struct 사용
        struct Static{
            static var instance: MyClass! = nil
        }
        // static 프로퍼티가 nil일 때만 객체 생성(한 번만 생성)
        if Static.instance == nil {
            Static.instance = MyClass()
        }
        return Static.instance
    }
}

let obj1 = MyClass.sharedInstance
let obj2 = MyClass.sharedInstance
obj1 === obj2 // true - 같은 객체
```


##### 연산자

- 기능 작성: 메소드
- 할당, 반환타입, 주석, 메소드 or 프로퍼티 접근용 점. 불가
- 기존의 or 새로운 연산자 정의
- 단일 연산자 vs 이진 연산자
- 연산자 위치(단일 연산자) - 전위, 후위
- 이진 연산자: 결합 순서
- 연산자는 타입 외부에 작성

```swift
class Point {
    var x: Int
    var y: Int
    init(x: Int, y: Int){
        self.x = x
        self.y = y
    }
}

func +(left: Point, right: Int) -> Point {
    return Point(x: left.x + right, y: left.y + right)
}

func +(left: Point, right:(Int, Int) )->Point{
    return Point(x: left.x + right.0, y: left.y + right.1)
}

var p1 = Point(x:1, y:1)
p1 + 2 // x:3, y:3
p1 + (3, 5) // x: 4, y: 6
```

- 전위연산자 
 - prefix operator
- 후위연산자
 - postfix operator
 
 
##### 새로운 연산자 
- 새로운 연산자 선언: operator
- 연산자 위치: prefix, infix, postfix
- 연산자 우선 순위: precedence

```swift
infix operator **{associativity left precedence 145}
```

- 다른 연산자와 precedence를 같게 만들면 결합 순서도 신경써야 함

##### 비교
- 같음 비교 
 - 객체 주소 비교: ===, 레퍼런스 타입에만 사용
 - 객체 내용 비교: = =연산자



 