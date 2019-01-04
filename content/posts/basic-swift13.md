+++
title = "Swift13 - Foundation Framework 01"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-20T12:02:59-06:00"
menu = "main"
+++

#### 프레임워크와 클래스

- OS X, iOS용 어플리케이션의 기본 프레임워크
- 클래스 접두어 NS~
 - 데이터 다루기
 - 네트워크
 - 파일처리
 
> Object C와 프레임워크
>
>- Swift 언어 발표 이전 개발 언어로 Object C
>- 많은 프레임워크가 Object C 형태로 제공
>- API 특징
>  - Failable Initializer
>  - AnyObject 타입


##### NSObject

- 파운데이션 프레임워크 중 가장 기본 클래스
- 메모리 관리
- 타입 체크 : AnyObject 타입 사용하면 검사 필요
- 객체 비교 : isEqual
     - func isEqual(object : AnyObject?) -> Bool
     - 파운데이션의 기능 구현 -> Swift의 기능에도 동작
     - isEquals 메소드 작성하면 ==로도 사용
     
```swift
// 객체 비교
class Rectangle : NSObject {
    var width : Int
    var height : Int
    
    init(width : Int, height : Int){
        self.width = width
        self.height = height
    }
    
    override func isEqual(object: AnyObject?) -> Bool {
        if let another = object as? Rectangle{
            return self.width == another.width && self.height == another.height
        }
        return false
    }
}

var obj1 = Rectangle(width : 10, height : 20)
var obj2 = Rectangle(width : 10, height : 20)
var obj3 = Rectangle(width : 30, height : 30)

obj1 == obj2    //isEqual 호출, true
obj1.isEqual(obj3) //isEqual, false
```

- 셀렉터 : 메소드 식별 정보
 - 사용자 이벤트에 반응하는 메소드
 - 타이머의 시간 간격에 호출하는 메소드
 - 알림이 발생하면 동작하는 메소드
 - 자주 사용
 - Select 구조체 사용
 - 메소드 식별 문자열을 집어넣음
 - 메소드 식별방법 : 메소드 이름과 파라미터 정보
      - 식별 문자열
           - 파라미터 유무에 따라 콜론으로 구별 //Selector("greeting:")
           - 파라미터 2개 이상시, 콜론과 외부 파라미터 이름으로 구별 //Selector("greeting:with:")
      - 메소드 호출 가능 여부 검사
           - func respondsToSelector(aSelector : Selector) -> Bool
 - Initializer의 셀렉터
     - 파라미터가 있는 경우
         - init**With** + 첫번째 외부 파라미터
         - Selector("initWithWho:emotion:")
         
```swift
class Rectangle : NSObject {
    var width : Int
    var height : Int
    
    init(width : Int, height : Int){
        self.width = width
        self.height = height
    }
    
    override func isEqual(object: AnyObject?) -> Bool {
        if let another = object as? Rectangle{
            return self.width == another.width && self.height == another.height
        }
        return false
    }
    
    func size() -> Int{
        return width * height
    }
    
    func setWidth(w : Int, height h : Int) {
        self.width = w
        self.height = h
    }
}

var obj1 = Rectangle(width : 10, height : 20)

let sizeSelector = Selector("size")
obj1.respondsToSelector(sizeSelector)
obj1.respondsToSelector("setWidth:height:")     // true
obj1.respondsToSelector("initWithWidth:height:")    //true
```

>**Selector()는 컴파일하고 나서야 에러 찾을 수 있음. 파라미터가 없는데 : 붙였다던지 등등**


##### NSLog

- 콘솔에 정보 출력
- 포맷 문자 : %a(객체 타입), %d(정수), %f(실수)

```swift
class Rectangle : NSObject {
    var width : Int = 0
    var height : Int = 0
   
    override var description : String {
        return "Rectangle \(width), \(height)"
    }
}

var obj1 = Rectangle()
NSLog("%@", obj2)   //Rectangle 0, 0
```


##### 데이터 다루기

- 문자열, 배열, 딕셔너리와 같이 데이터를 다루는 클래스 제공
 - 문자열 : NSString 
 - 배열 : NSArray 
 - 딕셔너리 : NSDictionary
- 데이터 변경 가능한(Mutable)클래스와 변경 불가능한 클래스(Immutable)
 - 파운데이션은 별도의 클래스
- Swift에서 제공되는 데이터 타입 : 밸류 타입
- 파운데이션 프레임워크가 제공하는 클래스 : 레퍼런스 타입
- 호환되는 부분이 많음

##### NSString

- NSString 클래스의 메소드 같이 사용
- 문자열 비교 : isEqualToString:
 - Swift 문자열과 호환됨
 
```swift
let switfStr = "string"
let nsSTr : NSString = "string"
swiftStr == nsStr //true
```

- 문자열 길이 : length
    - Swift 문자열 사용 불가
    
```swift
str.length // NSString만 사용
countElements(swiftStr) // Swift 문자열 길이
```

```swift
var str : String = "Hello Swift"
var str2 : NSString = "HI"
var str3 : String = str2    // String타입, "Hi"

str2.length
str3.length //Error
countElements(str3)
```

- 찾기
 - NSRange 객체가 반환
 
```swift
// 함수
func rangeOfString(aString : String) -> NSRange
func rangeOfString(aString : String, options mask : NSStringCompareOptions) -> NSRange

var nsStr : NSString = "Hello Swift"
let range = nsStr.rangeOfString("ell")
if range.location != NSNotFound {
	println("found")
else {
	println("not found")
}
```

- 문자열 가공
 - 문자열 자르기(substring)
     - NSString, String이 조금 다르게 동작(타입)
 - 문자열 덧붙이기
     - 트리밍
     - 문자열을 덧붙인 문자열
     
> 문자열 가공 부분은 API나 책 참고할 것

- 변환
 - 원시 타입 변환 (String 타입은 사용 불가)
 
```swift
let boolStr : NSString = "true"
let boolRet = boolStr.boolValue

let intStr : NSString = "123"
let intRet = intStr.intValue
```

- 대소문자 변환

```swift
var uppercaseString : String { get}
```

- 저장
 - 파일 저장
 - 에러 다루기 : NSErrorPointer
     - NSError 옵셔널 변수 생성
     - & 연산자와 사용
     
```swift
let filePath = "/Users/sdt5/Deskop/test.txt"
var error : NSError?
let ret = str3.writeToFile(filePath, atomically: true, encoding: NSUTF8StringEncoding, error: &error)

if ret {
    println("success")
}
else {
    println("fail. \(error?.localizedDescription)")
}
```

- 가변 문자열
 - NSMutableString 클래스
 - 문자열 수정하는 메소드, 변환타입 void
 - NSString : 불가변형
 
```swift
// 추가
func insertString(aString : String, atIndex loc : Int)
func appendString(aString : String)
// 삭제
func deleteCharactersInRange(range : NSRange)
// 새로운 문자열로 저장
func setString(aString : String)
```

- 레퍼런스 타입
 - **스위프트 문자열 String과 NSString의 가장 큰 차이 : 밸류 타입 vs 레퍼런스 타입**
 - NSMutableString으로 문자열 하나를 수정했을 때, 레퍼런스 타입이라 둘 다 바뀜
 - String은 바뀌지 않음
 
```swift

//NSMutableString
var mStr1 = NSMutableString(string: "Hello")
var mStr2 = mStr1

mStr1.appendString(" Foundation")
mStr1 // Hello Foundation
mStr2 // Hello Foundation

// String
var str1 = "Hello"
var str2 = str1

str1.append("c")

str1 // Helloc
str2 // Hello
```


##### 배열

- 파운데이션 : NSArray, NSMutableArray
 - 다양한 타입 다루기, AnyObject
 - ```let array2 : NSArray = ["sdsd", "sds", da"]```
- Swift : [Type] 
 - 한 종류의 타입으로 사용
 - ```let swiftArray = ["a", "b", "c"]```
- 파일 저장(XML형태로 저장. plist)과 복원
- 복원 - Failable Initializer
- 가변형 배열 NSMutableArray

```swift
var array = [1,2,3] //swift
var array2 : NSArray = [1,2,3] //NSArray

array.count
array.count
countElements(array)
//countElements(array2) //Error

var array3 : [AnyObject] = array2
var array4 = array2 as [Int]

//array.objectAtIndex(0) //Error
array2.objectAtIndex(1)

array2.writeToFile("/Users/sdt5/Desktop/array.plist", atomically: true)  // array.plist 파일 생성됨
```


##### 딕셔너리

- 파운테이션 : NSDictionary, NSMutableDictionary
- 딕셔너리 항목 접근
 - 첨자 표기
 - objectForKey:
 
```swift
var dic1 : [Int : NSObject] = [1: "a", 2 : "b", 3: "c"]
dic1[4] = 123
var dic2 : NSMutableDictionary = [1:"a", 2:"b", 3:"c"]
dic2[4] = 123

//dic1.objectForKey(1) //Error
dic2.objectForKey(4)
```

##### 날짜와 시간

- NSDateFormatter : 포맷에 맞는 날짜 표시
- 날짜와 시간 포맷

```swift
var now = NSDate()
var yester = NSDate(timeIntervalSinceNow: -3600*24) //어제 날짜

var formatter = NSDateFormatter()
formatter.dateStyle = NSDateFormatterStyle.MediumStyle
formatter.timeStyle = NSDateFormatterStyle.LongStyle

formatter.stringFromDate(now)

formatter.dateFormat = "yy/MM/dd"
```

- 로케일
 - NSLocale : 지역 고유의 표기법
 - 지역, 언어권, 언어코드 (US, ko_KR)
- 켈린더, 날짜 컴포넌트
 - NSCalendar, NSDateComponent
 
```swift
var calendar = NSCalendar.currentCalendar()
calendar.component(NSCalendarUnit.CalendarUnitWeekOfYear, fromDate: now)
```


##### 파일 다루기 

- NSFileManager : 파일 시스템을 다루는 기능
 - 폴더 내 파일 목록
 - 파일 존애 여부 확인
 - 파일 복사, 이동 삭제
- 파일 매니저 객체는 싱글톤 메소드를 이용해서 생성

```swift
class func defaultManager() -> NSFileManager
```

- 폴더 내 파일 목록 : func contentsOfDirectoryAtPath(..)

```swift
let dirPath = "/Users/sdt5/Desktop"
var error : NSError?
let fm = NSFileManager.defaultManager()
let contentsOfDir = fm.contentsOfDirectoryAtPath(dirPath, error: &error)

if let fileList = contentsOfDir {
    println("file list in \(dirPath)")
    for file in fileList {
        println("\(file)")
    }
}
else{
    println("Error : \(error)")
}
```

- 파일 복사, 이동, 삭제
 - 복사 : copyItemAtPath
 - 이동 : moveItemAtPath
 - 삭제 : removeItemAtPath
 
```swift
let fm = NSFileManager.defaultManager()
var error : NSError?

let originPath = "/Users/sdt5/Desktop/array.plist"
if fm.fileExistsAtPath(originPath){
    println("파일 존재")
    let copyPath = "/Users/sdt5/Desktop/copy"
    
    // 파일 복사
    var ret = fm.copyItemAtPath(originPath, toPath : copyPath, error: &error)
    if !ret {
        println("복사 실패 \(error)")
    }
    
    // 파일 삭제
    ret = fm.removeItemAtPath(copyPath, error: &error)
    if !ret {
        println("삭제 실패 \(error)")
    }
}
else{
    println("파일 없음")
}
```

- 파일 내용 읽기/쓰기
 - NSFileHandler 사용
 
>***나머지는 API문서 참고***

