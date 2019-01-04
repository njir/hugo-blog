+++
title = "Swift01 - 기본 문법과 타입"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19"
menu = "main"
+++

#### 기본문법

- 별도의 main함수를 작성하지 않는다.
- main 소스 코드의 글로벌 영역의 코드에서 시작한다.
- main이 아닌 다른 소스 파일에는 함수나 타입을 선언하는 코드만 작성한다.
- 세미콜론 생략 가능
- 변수의 값을 출력하려면 역슬래쉬(\)와 괄호 이용

```swift
var str = "hello"
println("\(str)")
```

- 연산자
 - 다른 언어와 달리 나머지 연산은 실수에서 사용가능하다.
 - 3.5 % 2 // = 1.5
- 범위 연산자
 - Closed Range(1...10) : 시작과 끝 모두 포함
 - Half Closed Range(1..<9) : 시작 포함, 종료값 포함X
- 변수 선언
 - 변수의 타입은 변수 이름 뒤에 콜론(:)을 이용
 
```swift
var i = 1
var f : Float = 1.1
```

- 변수의 타입이 정해지면 그 타입에 맞는 값을 입력해야함
- 정수형으로 선언된 변수에 실수 값 대입하면 컴파일 에러

```swift
var i = 1
i = 3.14 //Error
```

- 상수는 let을 이용해 선언

```swift
let constant = 234
```

- 데이터 타입
 - 정수형 타입에 Int8, Int16, Int32, Int64 사용
 - 이들 모두는 서로 다른 타입임(섞어서 사용X)
 - 변환 함수 사용
 
```swift
let var3 : Int16 = Int16(var1)
```

- 문자열
 - 문자열(String)에 문자(Character)를 덧붙이는 연산자는 타입 에러 발생
 
>현재 버전에서 문자열 덧불일 때 6개 이상은 에러 발생

```swift
var str1 = "\(1)"+"\(2)"+"\(3)"+"\(4)"+"\(5)"
var str2 = "\(1)"+"\(2)"+"\(3)"+"\(4)"+"\(5)"+"\(6)" // Error
```
- 문자열 길이
 - countElements(str)이라는 전역 함수
 - 버전에 따라 해당 함수명이 달라지고 있다.


##### 튜플

- 괄호를 이용해서 여러 값을 하나로 묶은 것으로 다양한 타입의 값을 다룰 수 있다

```swift
var one = (1, "asd", "가나다")
```

- 튜플을 구성하는 각 항목마다 이름을 선언할 수 있다

```swift
let two = (num : 2, eng : "two", kor : "둘")
// 이름으로 다음과 같이 접근 가능
two.num // 2
two.kor // "둘"
```

- _(언더스코어)를 이용하면 값을 얻어오지 않음

```swift
let (num, _, _) = two
num // 2
```

##### 배열

- 다수의 데이터를 인덱스로 다루는 타입
- var로 선언된 배열 변수는 배열의 내용 변경 가능
- let으로 선언하면 내용 변경 불가(Immutable)

```swift
var intArray : [Int] = [1, 2, 3, 4, 5]
let strArray = ["A", "B", "c"]
```

- **아무 원소도 포함하지 않는 공백 배열을 만드려면 다음과 같이 선언해야함**

```swift
var emptyArray = [Int]()
var emptyArray2 = [String]()
```

- 배열에 원소를 추가하려면 +연산자나 append 메소드 이용(마지막 인덱스에 추가)

```swift
var intArray : [Int] = [1,2,3]
intArray += [4]
intArray.append(5)

// 특정 위치에 추가하려면 or 삭제
func insert(newElement: T, atIndex : Int)
func removeAtIndex(index : Int) -> T
```

##### 딕셔너리

- Key-Value 방식으로 데이터 다루는 콜렉션 타입
- 딕셔너리에 저장된 키는 유일해야 함

```swift
var dic1 : [String:Int] = ["1월" : 1, "2월" : 2, "3월" : 3]
var dic2 = ["1월" : "January", "2월" : "February", "3월" : "March"]
// 접근
dic1["2월"] // 2
```

- 항목을 추가하려면 저장되지 않은 키를 이용해서 값 설정

```swift
dic1["4월"] = "april"
```

- 기존에 저장된 값을 이용해서 값을 입력하거나 updateValue 메소드를 이용하여 값 변경 가능

```swift
dic1["4월"] = "중간고사"
dic1.updateValue("계절의 여왕", forKey : "5월")
```

- 삭제하려면 다음과 같이 사용

```swift
dic1.removeValueForKey("4월")
dic1["3월"] = nil
```

##### if문

- if조건문에서 조건에는 반드시 부울 형태의 결과가 나타나도록 조건을 작성해야 한다.
- 조건을 감싸는 괄호는 생략 가능
- 바디의 중괄호는 생략 불가(한 줄이라도 괄호 써야함)

```swift
if 3 > 3 {
	println("3이 2보다 크다")
}
```

##### switch문

- 다른 언어와 좀 다르다
- 정수 타입 외에도 다양한 타입의 값을 제어식으로 사용 가능
- 제어식 조건에 맞는 첫 case를 하나만 실행하므로 break문장을 별도로 작성하지 않는다.
- switch에는 제어식의 모든 범위를 비교해야하므로 default를 작성해야한다.
- fallthrough는 조건에 맞는 case 실행 이후의 case도 비교

```swift
var someValue = 3
switch soemValue {
	case 1:
		prinln("1")
        fallthrough // 이후 case 도 비교
    default:
    	println("other")
}
```

- case에 문자열이나 튜플 등이 들어간 조건식을 작성할 수 있다.
- where를 이용해서 바인딩 조건을 추가로 작성 가능

```swift
var someValue = (3, 5)
switch someValue {
	case (1, 5) :
    	println("1")
    case (1...3 , 6..<8):
    	println("1~3, 6~7")
    case (5, let y):
    	println("5, \(y)")
    case let(x,y) where x==y:
    	println("x, y 동일한 값")
    default:
    	println("other")
}
```

##### 반복

- for, while문 사용
- 배열과 같은 콜렉션 타입인 경우 for-in 사용 가능

```swift
var str = "hello"
for c in str {
	println("\(c)")
}
```

- 딕셔너리의 모든 원소를 순회하려면 튜플을 이용해서 for-in 이용

```swift
for (kor, eng) in dic1 {
	println("Key : \(kor), Value : \(eng)")
}
```