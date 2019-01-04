+++
title = "Swift13 - Foundation Framework 02"
description = "T아카데미 Swift1.2 강좌 정리"
tags = [
    "swift",
]
date = "2015-03-20T12:22:59-06:00"
menu = "main"
+++


##### 직렬화

- 바이너리 형태로 변환
- 바이너리 데이터 다루기 : NSData
 - 직렬화된 데이터를 읽고, 파일로 쓰기
- 직렬화 담당 : 아카이버/언아카이버
 - 직렬화 : NSKeyedArchiver
 - 역직렬화 : NSKeyedUnarchiver
- 직렬화 프로토콜 : NSCoding

```swift
//파일로 저장
let filePath = "/Users/sdt5/Desktop/array.plist"
let ret = NSKeyedArchiver.archiveRootObject("String Data", toFile: filePath)

//파일에서 읽어오기
let str = NSKeyedUnarchiver.unarchiveObjectWithFile(filePath) as String
println("파일에서 복원 : \(str)")

//NSData 로 직렬화
let data = NSKeyedArchiver.archivedDataWithRootObject("String Data")

//NSData 에서 읽어오기
let str2 = NSKeyedUnarchiver.unarchiveObjectWithData(data) as String
println("NSData 에서 복원 : \(str2)")
```

- Key-Value 방식으로 직렬화
- **커스텀 클래스 직렬화**
 - NSCoding 프로토콜
 - 클래스에 프로토콜 채택
 - 인코딩/디코딩(init) 메소드 작성
 - 프로토콜 + init -> required
 
```swift
//저장
var obj = Person(name : "태연", birthyear : 1989)

let filePath = "/Users/sdt5/Desktop/array.plist"
let ret = NSKeyedArchiver.archiveRootObject(obj, toFile: filePath)
// 복원
let obj2 = NSKeyedUnarchiver.unarchiveObjectWithFile(filePath) as Person
println("name : \(obj2.name) - birthyear : \(obj2.birthyear)")
```

- 상속과 직렬화
 - 부모 클래스도 직렬화, 자식 클래스에서 super 호출
 - 인코딩/디코딩하는 코드에서 부모 클래스의 인코딩/디코딩 코드 호출해야 함
 

##### 타이머

- NSTimer
- 타이머 중지

```swift 
func invalidate()
```
```swift
class Alarm : NSObject {
    func ring(){
        println("Wake up!")
    }
}

var obj = Alarm()
obj.ring()

let timer = NSTimer.scheduledTimerWithTimeInterval(1.0, target: obj, selector: Selector("ring"), userInfo: nil, repeats: true) //1초 뒤에 ring method 실행 . 콘솔 기반은 테스트 힘듦
// 커맨드 라인툴의 환경상 바로 동작작
timer.fire()
```

##### 알림

- NSNotification
- 알림 센터(NSNotificationCenter)를 이용, 알림 방송(notifier)
- 알림 센터에 알림 청취(receive) - 알림 감시자 등록

```swift
//알림 센터와 알림 이름
let notiCenter = NSNotificationCenter.defaultCenter()
let NotiName = "CustomNotification"

//알림 감시용 클래스 생성. NSObject 상속 필요
class MyClass : NSObject {
    func handleNoti(noti : NSNotification){
        println("알림 발생!")
    }
}

//알림 센터에 알림에 대한 감시자 객체 등록
let obj = MyClass()
notiCenter.addObserver(obj, selector: Selector("handleNoti:"), name: NotiName, object: nil)

//알림 발송
notiCenter.postNotificationName(NotiName, object: nil)
//알림 발송(알림 객체로)
let noti = NSNotification(name: NotiName, object: obj)
notiCenter.postNotification(noti)
```


##### 멀티쓰레드

- 사용자의 경험자 경험
- 기다리면 -> 홈버튼
- NSThread
 - 멀티쓰레드에서 셀렉터 실행
 - 현재 동작 중인 쓰레드 정보
 - 메인 혹은 쓰레드 확인
 
```swift
class MyThread : NSThread {
    override func main(){
        for var i = 0 ; i < 10 ; i++ {
            println("\(name) - \(i)")
        }
    }
}

var thread1 = MyThread()
thread1.name = "thread1"
var thread2 = MyThread()
thread2.name = "thread2"

thread1.start()
thread2.start()

while(true){}
```
 
- NSOperation, NSOperationQueue
 - 쓰레드 큐
 - 동시 동작 쓰래드 갯수 조절
 - 쓰레드 대기/동작
 - 큐를 이용한 쓰레드 관리
 
```swift
class MyThread : NSOperation {
    var title : String!
    override func main(){
        for var i = 0 ; i < 10 ; i++ {
            println("\(title) - \(i)")
        }
    }
}

var thread1 = MyThread()
thread1.title = "thread1"
var thread2 = MyThread()
thread2.title = "thread2"
var thread3 = MyThread()
thread3.title = "thread3"

var queue = NSOperationQueue()
queue.maxConcurrentOperationCount = 2
queue.addOperation(thread1)
queue.addOperation(thread2)
queue.addOperation(thread3)

while(true){}
```


##### 네트워크

- NSURL : 리소스 위치 정보
 - 지역 파일, http(s) URL 다루기
 - URL에 허용되지 않는 문자 - 한글 -> 인코딩
- NSURLRequest : 리소스 요청
- NSURLResponse : 요청에 대한 응답
- NSURLConnection : 연결
- 리소스 얻기
 - NSData 객체로 생성
 
```swift
let urlStr = "http://images.apple.com/v/imac-with-retina/a/images/overview/intro_fallback_xlarge.jpg"
let url = NSURL(string : urlStr)!

if let data = NSData(contentsOfURL : url){
	println("size : \(data.length)")
    let image = NSImage(data: data)
}
```

