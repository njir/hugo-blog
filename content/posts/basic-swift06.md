+++
title = "Swift06 - 메모리 관리(ARC)"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T18:50:59-06:00"
menu = "main"
+++

#### 메모리

##### 메모리 관리

- 객체 생성 -> 메모리 차지
- 메모리 공간의 제약 -> 메모리 관리
- 메모리 관리
 - 필요한 객체 유지
 - 필요없는 객체 해제
- 사용 중이라는 표시 - own(소유하기)
- 객체 소유하는 방법 : 강한 참조(Strong pointer)

##### 객체 소유 관리

- 레퍼런스 카운트 - 강항 참조의 갯수 
 - 객체 사용 -> 레퍼런스 카운트 늘리기
 - 객체 사용 끝 -> 레퍼런스 카운트 감소
 - 레퍼런스 카운트가 0이면 사용 끝
 
##### ARC

- 객체 사용/사용 완료 - 레퍼런스 카운트 증가와 감소
 - 수동 관리
 - 자동 관리
- Automatic Reference Counting : 자동 레퍼런스 카운트 관리
 - 객체의 레퍼런스 관리 코드 자동 생성(컴파일)
 - 클래스에만 적용
 - 밸류 타입(구조체, enum)에는 미적용
 
##### 객체 소유와 해체

- 객체 생성 - 객체 소유

```swift
var ptr : MyClass? = MyClass() //레퍼런스 카운트가 1 증가
```

- 소유 해제 : 옵셔널로 선언

```swift
ptr = nil //레퍼런스 카운트 1 감소
```

- 해제 확인하기 : deinit

```swift
class test{
    deinit{
        println("객체가 메모리에서 해제")
    }
}

var testObj : test? = test()
testObj = nil   //객체가 메모리에서 해제
```

##### 객체를 소유하는 포인터 - 강한 참조

- 소유권 해제가 객체의 해제는 아니다

```swift
var anotherPointer = obj
```

- 강한 참조의 문제
 - 두 클래스에서 상호 참조(두개 이상의 관계에서도 가능)
 - 서로 소유하므로 해제되지 않음(메모리 누수)
 - 수동으로 해제되도록 작성해야 함
 
```swift
class MyClass {
    var ptr : MyClass2?
    deinit {
        println("My Class 객체 해제")
    }
}

class MyClass2 {
    var ptr : MyClass?
    deinit {
        println("MyClass2 객체 해제")
    }
}

var obj : MyClass? = MyClass()
var obj2 : MyClass2? = MyClass2()
obj?.ptr = obj2
obj2?.ptr = obj

obj = nil	// 객체가 해제되지 않음
obj2 = nil  // 객체가 해제되지 않음
```

##### 소유하지 않는 포인터

- 강한 참조 : 객체를 소유하는 포인터
- 약한 참조 : 객체를 소유하지 않는 포인터
- weak
    - 참조하던 객체가 해제되면 자동으로 nil
    - nil이 되므로 옵셔널
    - 상호 독립적으로 사용 가능
        - ex) 사용자와 스마트폰, 운전자와 자동차
         
```swift
class Student {
    var phone : Phone?
    deinit{
        println("Student 객체가 메모리에서 해제")
    }
}

class Phone {
    weak var owner : Student?
    deinit{
        println("Phone 객체가 메모리에서 해제")
    }
}

var owner : Student? = Student()
var iphone : Phone? = Phone()

iphone!.owner = owner
owner!.phone = iphone

owner = nil  // Student 객체가 메모리에서 해제"
iphone = nil // Phone 객체가 메모리에서 해제
```

- unowned
     - 참조하던 객체가 해제되도 nil로 변하지 않음
     - 옵셔널로 선언하지 않음 -> 에러 조심!
     - 완전히 종속적인 경우에 사용
         - ex) 신용카드와 사용자, 국가와 도시
         
```swift
 class Person {
    var card : CreditCard?
    deinit {
        println("Person 객체 해제")
    }
}

class CreditCard {
    unowned var owner : Person
    init(owner : Person){
        self.owner = owner
    }
    deinit {
        println("CreditCard 객체 해제")
    }
}

var person : Person? = Person()
var card : CreditCard? = CreditCard(owner : person!)
person = nil
card = nil
```
     
##### 콜렉션과 소유권

- 콜렉션에 객체 저장
 - 콜렉션이 객체 소유
- 콜렉션에서 객체 삭제
 - 소유권 해제
- 콜렉션 객체 해제
 - 소유권 해제
 
```swift
var owner2 : Student? = Student()
var owner3 : Student? = Student()
var array = [owner2, owner3]

owner2 = nil // 객체가 해제되지 않는다.
array.removeAtIndex(0) // Student 객체가 메모리에서 해제"
```
 
