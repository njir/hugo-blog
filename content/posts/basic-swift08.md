+++
title = "Swift08 - 구조체"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T20:50:59-06:00"
menu = "main"
+++

##### 구조체

- 클래스처럼 프로퍼티와 함수를 선언
- 클래스와 구조체
 - 같은점 : 데이터, 행위
 - 다른점 : 상속, 레퍼런스vs밸류, ARC(레퍼런스 타입만)
- 구조체 정의

```swift
////// 1
struct Point {
    var x = 0
    var y = 0
    
    func description() -> String {
        return "point : \(x), \(y)"
    }
}

var p1 = Point()
p1.x = 10
p1.y = 20
var p2 = Point(x : 3, y : 5)

//// 2
struct MyStruct {
    var var1 : Int = 0
}
class MyClass {
    var var1 : Int = 0
}
var struct1 = MyStruct()
var struct2 = MyStruct(var1 : 10)
var obj = MyClass()
```

- 구조체도 객체 생성되면 - 초기화 필요
- 초기화 메소드 작성
 - 구조체도 클래스와 마찬가지로 초기화 과정 필요
 - 상속 없으므로 부모 클래스의 초기화 위임 없음
 - convenience initializer : convenience 생략
 
```swift
 struct Point {
    var x = 0
    var y = 0
    
    init(){
        self.x = 0
        self.y = 0
    }
    
    func description() -> String {
        return "point : \(x), \(y)"
    }
}
```

- 정적 메소드와 프로퍼티 : static
 - 객체를 생성하지 않고 사용하는 메소드
 
```swift
struct Point {
    var x = 0
    var y = 0
    
    static func newPoint(x : Int, y : Int) -> Point {
        var newObj = Point(x: x, y: y)
        return newObj
    }
  }
```

- 구조체의 데이터
 - 초기화 메소드에서 설정 가능
 - 구조체 외부에서 설정 가능
 - **구초제 내부에서 설정 불가**
       - mutating 으로 내부에서 데이터 설정
       
```swift
struct MyStruct {
    var var1 : Int = 0
    var var2 : String
    init() {
        self.init(str : "Hello")
    }
    init(str : String){
        var2 = str
    }
    static var var3 = "Struct"
    mutating func increase(){
        var1++
    }
}

class MyClass {
    var var1 : Int = 0
    var var2 : String
    convenience  init() {
        self.init(str : "Hello")
    }
    init(str : String){
        var2 = str
    }
}

var struct1 = MyStruct()
var struct3 = struct1
struct1.var1 = 10   //var1 : 10, var2 : "Hello"
struct3 //var1 : 0, var2 : "Hello"

var obj = MyClass()
```

- struct, array, dictionary .. 밸류 타입

```swift
var array = [1,2,3]
var array2 = array
array += [4] //[1, 2, 3, 4]
array2          // [1,2,3]
```

##### Enum

- 열거형(Enumeration Type)
- 옵션 중 하나의 값
- 타입이 있는 Enum은 case로 할당된 값으로 다룰 수 있다
- 범위 벗어나면 nil -> 옵셔널

```swift
var ael = Pet(rawValue : 0)!
ael.rawValue
```

- 계산프로퍼티, 메소드 사용 가능
  - 저장 프로퍼티는 불가능
- Association

```swift
enum Day : Int {
    case AM
    case PM
    func desc() -> String{
        switch self{
        case .AM :
            return "오전"
        case .PM :
            return "오후"
        }
    }
}

Day.AM
Day.PM
let e1 = Day(rawValue : 0)
Day.AM.rawValue
Day.AM.desc()   //"오전"
```

- 프로퍼티와 메소드

```swift
enum Pet : Int {
    case Cat = 0
    case Dog
    case Other
    
    var name : String{
        switch self {
        case .Cat:
            return "고양이"
        case .Dog :
            return "개"
        default:
            return "기타"
        }
    }
    
    func description() -> String {
        return self.name
    }
}

var raz = Pet.Cat
raz.name    //고양이
raz.description()  //고양이
```
