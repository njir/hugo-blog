+++
title = "Swift03 - 함수"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-19T14:35:59-06:00"
menu = "main"
+++

#### 함수

- -> 로 결과 타입 반환

```swift
// Bool 반환
func areYouOK() -> Bool {
    return true
}
// String
func favorite() -> String{
    return "Coffee"
}
```

- 다수의 값 반환 : 튜플 사용

```swift
func random3() -> (Int, Int, Int){
    let r1 = Int(arc4random() % 10)
    let r2 = Int(arc4random() % 10)
    let r3 = Int(arc4random() % 10)
    return (r1, r2, r3)
}
// 다음과 같이 튜플로 얻어서 사용
var (num1, num2,  num3) = random3()
println("Random numbers : \(num1), \(num2), \(num3)")
var nums = random3()
println("Random numbers : \(nums.0), \(nums.1), \(nums.2)")
```

##### 파라미터

- 내부 파라미터

```swift
func greeting(person : String, emotion : String){
    println("person : \(person), emotion : \(emotion)")
}
greeting("zzzz", "kkk")
```

- 외부 파라미터 이름

```swift
func greeting(whom person : String){
    println("\(person)")
}
greeting(whom : "My Friend")


func externalParamFunc(whom person : String, with emotion : String){
    println("Hello " + person + "with " + emotion)
}
// 외부 파라미터 있는 함수를 호출할 때 해당 파라미터 호출
externalParamFunc(whom : "myfiend", with :"smile")
```

- 내부 파라미터 이름을 외부 파라미터 이름으로 : # 사용

```swift
func greeting(# person : String, # emotion : String){
    println("person : \(person), emotion : \(emotion)")
}
greeting(person : "aaaa", emotion : "bbb")
```

- 파라미터 기본값

```swift
func externalParamFunc2(whom person : String, emotion : String = "Happy"){
    println("Hello " + person + "with " + emotion)
}
externalParamFunc2(whom : "myfiend")
```

>**혼동 주의**
```swift
func greeting(whom person : String, emotion : String = "Happy"){}
func greeting(whom persoon : String){}
greeting(whom : "World")	// 어떤 함수 호출?
```
 

- 외부 파라미터 이름
 - 자동으로 내부 파라미터 이름
 - 수동으로 외부파라미터 설정하기
 - _ (언더스코어) 사용
   
   
##### 다수의 파라미터

- 하나의 파라미터로 다수의 값 설정
- ... 이용해서 선언
- 함수의 파라미터 중 마지막에 위치

```swift
func addAll(values : Int...) -> Int {
    if values.count == 0 {
        return 0
    }
    else {
        var result = 0
        for v in values {
            result += v
        }
        return result
    }
}

addAll(1,2,3)
```

##### 파라미터 변수와 상수

- 평소에는 let으로 간주
- var 키워드로 함수 안에서 변수로 사용 가능
- 값이 복사가 됨

```swift
func countTo(var value : Int) -> Int {
    var result = 0
    while value > 0 {
        result += value
        value--
    }
    return result
}

var count = 10
countTo(count)  // 55
count 			//10
```


##### inout 파라미터(레퍼런스)

- 함수 내에서 변경한 내용을 함수 외부에 반영되도록 하기 위해
- c에서의 레퍼런스

```swift
func swapTwoValue(inout var1 : Int, inout var2 : Int){
    var temp = var1
    var1 = var2
    var2 = temp
}
var value1 = 10
var value2 = 20
swapTwoValue(&value1, &value2)
value1      // 20
value2      // 10

```

##### 옵셔널 반환 타입과 파라미터

- 파라미터 : 옵셔널 가능
- 반환값 : 옵셔널 가능

```swift
func toInt() -> Int? 
```

- 파라미터로 전달된 값을 숫자로 변경할 수 엇으면 실패의 의마로 nil을 반환
- nil 반환이 가능하므로 반환 타입은 옵셔널
- ? 이나 ! 사용
- 아래는 if-let을 이용한 코드

```swift
func convertInt(str : String) -> Int? {
    if let result = str.toInt() {
        return result
    }
    else {
        return nilHandleErr
    }
}

if let num = convertInt("abc"){
    num * 3
}
else {
    println("숫자 아님")
}

let nu = convertInt("123")
println(nu * 10)
```

##### 함수 다른 예제

```swift
func adds(values : Int...) -> Int {
    var count = values[0]
    var sum = 0
    for var i = 0; i < count ; i++ {
         sum += values[i]
    }
    return sum
}

adds(1,1, 2,3)  //1
adds(3,1,2,3,4) //6
adds(4,1,2,3,4,5,6)//10



func add(var1 : Int, var2 : Int, inout ret : Int){
    ret = var1 + var2
}
var ret = 0
add(1,2, &ret)
println(ret)
```


