+++
title = "Swift07 - 상속"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T19:50:59-06:00"
menu = "main"
+++

#### 상속

- 기존 클래스에서 새 클래스 작성
- 프로퍼티, 메소드 그대로 사용

```swift
class [자식 클래스] : [부모 클래스] {}
```

- 재정의하기 : override
 - 메소드
 
```swift
 // 자식 클래스에서
override func size() -> Int {
    return width * width
}
```

 - 프로퍼티
 
```swift
override var width : Int {
        didSet {
            if height != width {
                height = width
            }
        }
    }
override var isSquare : Bool {
	get {
    	return true
    }
}
```

- override 누락하면 에러
- 재사용이 아닌데 override 사용하면 에러
- final : 상속을 방지하거나 메소드, 프로퍼티 오버라이딩 방지할때
- super : 부모 클래스를 참조하기 위한 포인터

```swift
class Parent {
    func description() -> String {
        return "부모클래스"
    }
}

class Child : Parent {
    func printDescription(){
        println("super.description :  \(super.description())")
        println("self.description :  \(self.description())")
    }
}

var obj = Child()
obj.printDescription()
//super.description :  부모클래스
//self.description :  부모클래스
```

```swift
class Parent {
    func description() -> String {
        return "부모클래스"
    }
}

class Child : Parent {
    override func description() -> String {
        return "자식클래스"
    }
    func printDescription(){
        println("super.description :  \(super.description())")
        println("self.description :  \(self.description())")
    }
}

var obj = Child()
obj.printDescription()
//super.description :  부모클래스
//self.description :  자식클래스
```

##### 상속과 초기화 메소드

- 상속 : 부모 클래스의 프로퍼티 상속
- 초기화 메소드 : 프로퍼티 초기화 필수
- 자식 클래스의 초기화 메소드
 - 부모 클래스의 초기화도 필요 > 초기화 위임
- 부모 클래스의 초기화 메소드를 상속하는 경우. 2가지
 - 자식 클래스에 Designated Initializer를 작성하지 않은 경우
 - 부모 클래스의 모든 Designated Initializer를 재정의 -> Convenience Initializer 상속
 
```swift
class Parent {
    var a : Int
    // Designated Initializer
    init(a : Int){
        self.a = a
    }
    //Convenience Initializer
    convenience init(){
        self.init(a : 0)
    }
}
//별도의 초가화가 필요하지 않는 자식 클래스
class Child : Parent {
    var b = 100
}
// main.swift
var childObj1 = Child(a : 10) // 부모클래스의 Designated Initializer로 객체 생성
var childObj2 = Child() // 부모클래스의 Convenience Initializer로 객체 생성
```

##### 자식 클래스의 Initializer

- 자식 클래스의 Designated Initializer

```swift
init([파라미터]){
	// 자식 클래스의 프로퍼티 초기화
    // 부모 클래스의 Designated Initializer 호출
    // 자식 클래스의 나머지 초기화 동작
}
```

```swift
class Parent {
    var a : Int
    //부모의 Designated Initializer
    init(a : Int){
        self.a = 0
    }
}

class Child : Parent {
    var b : Int
    //자식클래스의 Designated Initializer
    init(a : Int, b : Int){
        //같은 클래스의 초기화 먼저
        self.b =  b
        //부모 클래스의 Designated Initializer 호출
        super.init(a : a)
    }
}
```

- 자식 클래스의 Convenience Initializer

```swift
convenience init([파라미터]){
	//같은 클래스의 초기화 메소드 호출
    //초기화 코드
}
```

##### 2단계 초기화(Two Phase Initializer)

- 객체를 안전하게 초기화하기 위해 2단계 초기화로 진행
- Phase1에서는 객체를 생성하는 시작단계로 객체를 저장할 메모리 공간을 확보하고 클래스 프로퍼티가 초기화된다.
- Phase2는 클래스 상속 구조에서 최상위 클래스의 프로퍼티 초기값 설정 이후부터 시작이다.


##### 초기화 메소드 재정의

- 초기화 메소드를 재정의하려면 overrice 지시자를 이용
- 자식 클래스에서 부모 클래스의 모든 designated initializer를 재정의하면 부모 클래스의 convenience Initializer를 상속받는다.

```swift
class Parent {
    var a : Int
    var width : Int = 0
    init(a : Int){
        self.a = a
    }
    convenience init(){
        self.init(a : 0)
    }
}

class Child : Parent{
    var b : Int
    init(b : Int){
        self.b = b
        super.init(a : 0)
    }
    override init(a : Int){
        self.b = 10
        super.init(a : a)
    }
}

var obj1 = Parent(a : 10)
var obj2 = Child(b : 20)
var obj3 = Child()
var obj4 = Child(a: 30)
```

- Required Initializer
  - 자식 클래스에서 부모 클래스의 초기화 메소드를 반드시 재정의하도록 하려면 부모 클래스의 초기화 메소드에 required 지시자를 붙인다. 
  - 자식 클래스는 부모 클래스에서 required로 선언된 모든 초기화 메소드를 재정의해야 한다.
- Failable Initializer
 - 초기화 조건을 검사하는 코드는 부모 클래스의 초가화 메소드 위임을 마친 이후에 작성한다.

##### 초기화 메소드 정리

- 초기화 메소드 위임에 관련된 규칙
 - Designated Initializer는 부모 클래스의 Designated Initializer를 호출한다.
 - Convenience Initializer는 같은 클래스의 다른 초기화 메소드를 호출한다.
 - Convenience Initializer는 초기화 메소드 위임을 따라 Designated Initializer를 호출한다.
- 클래스의 Designated Initializer는 부모 클래스의 Designated Initializer를 호출하기 전에 같은 클래스에서 작성한 모든 프로퍼티의 초기값을 설정한다.
- 부모 클래스에서 상속받은 프로퍼티는 부모 클래스의 초기화 메소드로 초기값을 설정하고 난 다음에 자식 클래스에서 초기값을 설정한다.
- Convenience Initializer는 다른 초기화 메소드로 객체 초기화를 위임해서 객체 초기화를 완료한 다음에 값을 설정하도록 작성한다.
- 초기화 메소드에서 객체 초기화가 끝나기 전 프로퍼티를 사용하는 다른 메소드 호출은 금지된다.

```swift
class Shape {
    var edge : Int
    
    init(numberOfEdge : Int){
        edge = numberOfEdge
    }
    
    func description() -> String {
        return "\(edge)각형"
    }
}

class Rectangle : Shape {
    var width : Double
    var height : Double
    
    init(width : Double, height : Double){
        self.width = width
        self.height = height
        super.init(numberOfEdge : 4)
    }
    
    convenience init(){
        self.init(width : 0, height : 0)
    }
    
   override func description() -> String {
        return "사각형 가로 \(width) 세로 \(height)"
    }
    
    func size() -> Double {
        return Double(width * height)
    }
}

class Triangle : Shape {
    var a = 0.0
    var b = 0.0
    var c = 0.0
    
    init(a : Double, b : Double, c: Double){
        self.a = a
        self.b = b
        self.c = c
        super.init(numberOfEdge : 3)
    }
    
   override func description() -> String {
        return "삼각형 \(a), \(b), \(c)"
    }
    
    func size() -> Double{
        let s : Double = (a + b + c) / 2
        let result = sqrt(s * (s - a) * (s - b) * (s - c))
        return result
    }
}

var obj1 = Rectangle(width: 10, height: 20)
var obj2 = Rectangle()
obj2.width = 20
obj2.height = 10

var obj3 = Triangle(a: 3, b: 4, c: 5)
```



