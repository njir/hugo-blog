+++
title = "Swift09 - 프로토콜"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T20:55:59-06:00"
menu = "main"
+++

#### 프로토콜

- 메소드 호출 규격

```swift
protocol Singing {
    func sing() //선언만
}
```

- 메소드의 선언만 작성
- 인터페이스
 - 구현이 없어서 단독 사용 불가
 - 메소드 호출 약속
- iOS 어플리케이션 개발에서 자주 사용되는 패턴
- 상속과 함께 사용하면 부모클래스를 첫 번째로
- 프로토콜 정의, 클래스 채택 - override 아님
- 다중 프로토콜 채택 가능
 - 메소드 충돌되지 않도록 주의
 
```swift
protocol Species {
    class func species() -> String
}

class Cat : Singing, Species {
    func sing(){
        println("cat cat")
    }
    class func species() -> String {
        return "고양이"
    }
}
```

- 프로토콜 내 타입 메소드
 - 클래스 : class를 이용한 타입 메소드
 - 구조체와 enum : static을 이용한 타입 메소드
 
```swift
protocol Species {
    class func species() -> String
}

class Cat : Singing, Species {
    func sing(){
        println("cat cat")
    }
    class func species() -> String {
        return "고양이"
    }
}

struct Cow : Species {
    static func species() -> String {
        return "소"
    }
}
```

##### 프로퍼티

- 프로퍼티 선언 : 프로퍼티의 get/set 행위 선언
- 클래스 채택
 - 계산 프로퍼티
 - 저장 프로퍼티
 
```swift
protocol Singing {
    func sing() //선언만
    var duration : Int { get }
    var volume : Float { get set}
}

class Bird : Singing {
    func sing() {
        println("sing!")
    }
    var duration : Int {
        get {
            return 0
        }
    }
    var volume : Float = 0.0
}
```

##### 프로토콜에 초기화 메소드 선언

- 구현시 **required** 
- 프로토콜 내 failable initializer 선언

```swift
protocol Named {
    init(name : String)
}

class Monster : Named {
    let name : String
    required init(name : String){
        self.name = name
    }
}
```

##### 프로토콜 타입

- 프로토콜을 타입으로 사용

```swift
var singingAnimal : Singing = Human()
singingAnimal.sing()
```

- 프로토콜에 작성한 메소드, 프로퍼티만 사용
- 타입 오류

##### 프로토콜 상속

- 다른 프로토콜 상속 가능

```swift
protocol Entertaining : Singing, Dancing {}

class Human : Entertaining {
    func sing(){
        println("sdsd")
    }
    func danve(){
        println("dancing")
    }
}
```

- Entertaining 포로토콜을 채택하고 Entertaining이 상속하는 모든 프로토콜에 선언한 모든 메소드를 작성해야함

##### 클래스 전용 프로토콜

- 클래스에서만 채택 가능(구조체, enum 에러)
- class 키워드 사용

```swift
protocol PROTOCOL_NAME : class, SUPER_PROTOCOL {
}
```

##### 필수 항목과 선택 항목

- 필수 항목 - 구현 안하면 에러
- 선택 항목 : optional
- @objc 키워드로 프로토콜 선언
 - objectC 코드에서 사용할 수 있도록 하는 키워드
 
```swift
@objc protocol Baking {
	func baking()	//필수 구현 메소드
    optional func makeDough() //선택 구현 메소드
}
```

- 다각형의 면적을 구하는 SizeMeasuable, 각 변의 길이가 같은 Equilateral 프로토콜

```swift
class Shape {
    var edge : Int
    
    init(numberOfEdge : Int){
        edge = numberOfEdge
    }
    
    func description() -> String{
        return "\(edge)각형"
    }
}

protocol SizeMeasurable{
    func size() -> Double
}

protocol Equilateral {
    var isEquilateral : Bool { get}
    init?(equilateral : Double)
}

class Rectangle : Shape, SizeMeasurable, Equilateral {
    var width : Double
    var height : Double
    
    init(width : Double, height : Double){
        self.width = width
        self.height = height
        super.init(numberOfEdge: 4)
    }
    
    var isEquilateral : Bool {
        return width == height
    }
    
    required convenience init?(equilateral : Double){
        self.init(width : equilateral, height : equilateral)
        if equilateral < 0 {
            return nil
        }
    }
    
    func size() -> Double{
        return Double(width * height)
    }
    
    override func description() -> String {
        return "사각형가로 \(width) 세로 \(height)"
    }
}

if let obj1 = Rectangle(equilateral: 20){
    println("\(obj1.description()), size : \(obj1.size()), 정사각형 : \(obj1.isEquilateral)")
    // 사각형가로 20.0 세로 20.0, size : 400.0, 정사각형 : true
}
```
