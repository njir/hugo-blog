+++
title = "Swift10 - 클로저"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T21:55:59-06:00"
menu = "main"
+++


#### 클로저

- 메소드이자 객체
- Object C에서는 블록
- 클로저를 파라미터로 사용하는 것은 파라미터의 타입이 파라미터 -> 번환타입 형태

```swift
func sayHello() -> String {
	return "Hello"
}
var v1 = sayHello()
var v2 = sayHello	// v1 과 차이점이 무엇?
println(v1)
println(v2())
```

- 반환값으로

```swift
func sayHello() -> String {
	return "Hello"
}
func hello1() -> String {
	return sayHello()
}
func hello2() -> ???? {	//void->String or ()->String
	retrn sayHello
}
```

- 파라미터로

```swift
func sayHello() -> String {
	return "Hello"
}
func hello3(arg : String) {}
func hello4(arg : ????) {}	//void->String or ()->String
	
hello3(sayHello())
hello4(sayHello)
```

##### 클로저 사용하기

- 클로저를 사용하는 API
- Array의 sort메소드

```swift
func sort(isOrderedBefore : (T, T) -> Bool)
```

- 클로저 정의

```swift
{ (Parameters) -> RETURN_TYPE in
	// 클로저 동작 코드
}
```

```swift
(Int, Int) -> Int
func add(i : Int, b : Int) -> Int {
	return i + b
}
```

##### 클로저 사용

- 타입 : (T, T) -> Bool
- 파라미터 타입 정의 생략 가능
- 1줄 return인 경우 -> return 생략'
- 파라미터 선언 생략
 - $0, $1, $2로 파라미터 사용
- Tailing Closure

```swift
array.sort({(a: Int, b: Int} -> Bool in return a<b})
array.sort({(a, b} -> Bool in return a<b})
array.sort({(a, b} -> Bool in a<b})
array.sort({$0 < $1})
array.sort{$0 < $1}
```

##### 클로저 정의하기

- 두 값을 더하는 함수

```swift
func add(i : Int, j : Int) -> Int{
	let sum = i + j
    return sum
}
let ret = add(1,2)
println("1+2 = \(ret)")
```

- 이 함수에 클로저를 파라미터로 추가
- 결과를 처리하는 클로저 추가
- 비동기 방식으로 api를 만들때 handler 이용

```swift
func add(i : Int, j : Int, handler: (Int) -> Void){
	let sum = i + j
    handler(sum)
}
//사용
add(3,4 {(return : Int) -> Void in
	println("3+4 = \(result)")
})
//trailing closure
add(5,6) {(result)-> Void in
	println("5+6 = \(result)")
}
```

- 클로저 자체가 옵셔널
- 클로저가 반환타입인 함수
 - 함수 내 함수 정의 가능
 - 함수는 클로저의 한 형태
 - inline방식 가능