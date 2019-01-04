+++
title = "Swift02 - 옵셔널"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T14:15:59-06:00"
menu = "main"
+++

#### 옵셔널 정리

##### nil

nil : 값이 할당되지 않은 상태
nil에 함수나 프로퍼티 사용 -> 런타임 에러

변수와 상수를 사용하기 전에 값이 설정돼야한다.

```swift
var i : Int // Error
println(i)
```

일반 변수(상수)는 nil이 될 수 없다.

```swift
var str = "Hello"
str = nil //Error
```


##### 옵셔널

- nil이 될 수 있는 변수
- 타입 뒤에 물음표(?)로 선언

```swift
var optionalVar : Int?
```

- 타입 선언 필수
- 옵셔널은 일반 타입과 다르다.
- **메소드 호출, 프로퍼티 사용 못함**

```swift
var i2 : Int? = 3
println(i2) //nil

//successor : 다음 숫자를 알려줌
i2.successor() // Error. i2는 nil이다

var i3 = i2?.successor() // optional type
var i4 = i2!.successor() // not optional type
```

##### 옵셔널 변수 사용하기

- nil인가? 아닌가? -> if 조건문 사용
- if-let : nil 아니면 - 값 접근

```swift
if let realStr = optionalStr {
	// realStr은 옵셔널이 아니다.
    println("string : \(realStr)
}
else{
 	// optionalStr이 nil인 경우
}
```


##### nil 결합 연산자 : ??
- 해당 변수가 nil 이면 ?? 연산자 이후의 값 사용

```swift
var userSelectedColor : String?
//userSelectedColor가 nil 이면 Red
var colorName = userSelectedColor ?? "Red"  //colorName : Red
userSelectedColor = "Blue"
var colorName2 = userSelectedColor ?? "Red"  //ColorName : Blue
``` 


##### 옵셔널 체인, 강제 언래핑

- 옵셔널 체인 : 객체가 nil아면 더 이상 객체에 접근하지 않고 nil 반환
- 강제 언래핑 : 객체에 접근한 결과는 옵셔널 타입이 아니지만, 객체가 nil인 경우 에러 발생

```swift
//1. 옵셔널 체인
var optionalStr2 : String? = "abcd"
var num1 = optionalStr?.uppercaseString 
// nil이면 뒤에꺼 실행 안함. nil이 될수도 있음
// 이후에도 num1은 옵셔널임

//2. 강제 언래핑(Forced Unwrapping)
var num2 = optionalStr!.uppercaseString
optionalStr2 = nil

let str3 = optionalStr!.uppercaseString  //Error 발생. optionalStr2가 nil
```


```swift
var optionalStr : String?
optionalStr = "abcd"
let str1 = optionalStr?.uppercaseString //이후 str1은 nil 가능

var optionalStr : String?
optionalStr = "abcd"
let str2 = optionalStr!.uppercaseString //이후 str1은 무조건 nil이 아님
```

##### 암시적 언래핑 옵셔널

- ? 대신 ! 사용

```swift
var optionalStr : String!
optionalStr = "123"
optionalStr.toInt() //Success

optionalStr = nil
optionalStr.toInt() //Error
```

##### Conclusion

- 옵셔널 : nil 가능한 변수. 타입?
- 사용방법 : 변수?, 변수!
- 옵셔널 바인딩, 강제 언래핑
- 암시적 언래핑 : 타입!
- ***코드 자동완성 이용하자***


 
