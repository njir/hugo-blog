+++
title = "RxJS Operators"
description = "RxJS 자주 쓰이거나 꼭 알아야할 operator 간단하게 정리"
tags = [
    "rxjs",
    "Reactive",
    "javascript"
]
date = "2018-03-22"
menu = "main"
+++

# Operators of RxJS

참고로 모든 operator는 새로운 Observable을 리턴하게 된다.

## Utility

### do

`do`는 새로운 데이터가 emit 될 때마다 매번 실행된다. output처럼 do 데이터와 map 데이터가 서로 다르다.

```javascript
Rx.Observable.range(1, 5)
    .do(a => console.log(`From do ${a}`))
    .map(a => a * a)
    .subscribe(createSubscriber('simple'));

// output
//
// From do 1
// simple.next 1
// From do 2
// simple.next 4
// From do 3
// simple.next 9
// From do 4
// simple.next 16
// From do 5
// simple.next 25
// simple.complete
```

### finally

`finally`는 subscribe가 완료(complete)된 후에 실행된다.

```javascript
Rx.Observable.range(1, 5)
    .finally(a => console.log('From finally'))
    .map(a => a * 2)
    .subscribe(createSubscriber('finally'));
    
// output
//
// finally.next 2
// finally.next 4
// finally.next 6
// finally.next 8
// finally.next 10
// finally.complete
// From finally
```

***

## Filtering

### filter

말그대로 `filter`의 조건에 만족하는 Observable의 데이터만 emit한다.

```javascript
Rx.Observable(1, 10)
    .filter(a => a < 5)
    .subscribe(createSubscriber('filter'));
    
// output
//
// filter.next 1
// filter.next 2
// filter.next 3
// filter.next 4
// filter.complete
```

### first, last

Observable 데이터의 첫 번째 혹은 마지막 데이터만 처리
각 operator에서 공통적으로 알아야할 점은 next()가 없으면 ```EmptyError```가 발생한다.

```javascript
const simple$ = new Rx.Observable(observer => {
    observer.next(1);
    observer.next(2);
    observer.next(3);
    observer.next(4);
    observer.complete();
});

simple$.first()
    .subscribe(createSubscriber('first'));

// output
//
// first.next 1
// first.complete

simple$.last()
    .subscribe(createSubscriber('last'));

// output
//
// last.next 4
// last.complete
```

### single

`single` operator는 next를 한번만 emit해야 한다. 그렇지 않으면 에러가 발생한다.

```javascript
const single$ = new Rx.Observable(observer => {
    observer.next(1);
    observer.next(2);
    observer.next(3);
    observer.next(4);
    observer.complete();
});

single$.single()
    .subscribe(createSubscriber('single'));

// output
//
// single.error Sequence contains more than one element
// single.complete
```


### take, skip

- `take`: observable에서 `take`의 인자 만큼 data를 처리한다.<br>
- `skip`: 말그대로 data를 skip한 후 나머지 데이터만 처리한다.

```javascript
const simple$ = new Rx.Observable(observer => {
    observer.next(1);
    observer.next(2);
    observer.next(3);
    observer.next(4);
    observer.complete();
});

simple$.take(2)
    .subscribe(createSubscriber('take'));

// output
//
// take.next 1
// take.next 2
// take.complete
```

### skipWhile, takeWhile

- `skipWhile`: 해당 expression이 false인 데이터는 건너뛴 후 데이터를 emit.
- `takeWhile`: skipWhile과 유사하지만 expression이 true일 때까지 데이터 emit

```javascript
Rx.Observable.interval(500)
    .skipWhile(i => i < 4)
    .takeWhile(i => i < 8)
    .subscribe(createSubscriber('skipWhile'));

// output
//
// skipWhile.next 4
// skipWhile.next 5
// skipWhile.next 6
// skipWhile.next 7
// skipWhile.complete
```

### skipUntil, takeUntil
`skipUntil`, `takeUntil`은 Observable, Subject 데이터를 인자로 갖는다.

```javascript
Rx.Observable.interval(500)
    .skipUntil(Rx.Observable.timer(2000))
    .takeUntil(Rx.Observable.timer(4000))
    .subscribe(createSubscriber('takeUntil'));

// output
// 2초 후부터 print하고, 총 4초가 지나면 complete 된다.
// takeUntil.next 3
// takeUntil.next 4
// takeUntil.next 5
// takeUntil.next 6
// takeUntil.complete
```

***

## Combinations

### startWith

선언된 value부터 순서대로 emit한다.

```javascript
Rx.Observable(1,2,3)
    .pipe(startWith(0))
    .subscribe(createSubscriber('startWith'));
    
// output
//
// startWith.next 0
// startWith.next 1
// startWith.next 2
// startWith.next 3
// startWith.complete
```

### concat

두 개의 Observable을 combine하여 emit한다. 
`merge`와 달리 순서가 중요하다(output 참고). 

```javascript
Rx.Observable.range(1,5)
    .concat(Rx.Observable.range(10, 3))
    .subscribe(createSubscriber('concat1'));
    
// output
//
// concat1.next 0 
// concat1.next 1
// concat1.next 2
// concat1.next 10
// concat1.next 11
// concat1.next 12
// concat1.complete
```

### merge

두 개의 Observable을 하나의 Observable로 변환한다. 아래 예제처럼 두가지 방법으로 사용할 수 있다.

```javascript
Rx.Observable.interval(1000)
    .merge(Rx.Observable.interval(500))
    .take(5)
    .subscribe(createSubscriber('merge1'));

Rx.Observable.merge(
    Rx.Observable.interval(1000).map(i => `${i} secondes`),
    Rx.Observable.interval(500).map(i => `${i} half secondes`))
    .take(8)
    .subscribe(createSubscriber('merge2'));
    
// output
//
// merge2.next 0 half seconds
// merge2.next 0 seconds
// merge2.next 1 half seconds
// merge2.next 2 half seconds
// merge2.next 1 seconds
// merge2.next 3 half seconds
// merge2.next 4 half seconds
// merge2.next 2 sec
// merge2.complete
```

#### concat vs merge 

위 ```merge``` output 참고(순서가 다름)

```javascript
Rx.Observable.concat(
    Rx.Observable.interval(1000).map(i => `${i} secondes`).take(3),
    Rx.Observable.interval(500).map(i => `${i} half secondes`).take(3))
    .subscribe(createSubscriber('concat2'));
    
// output
//
// concat2.next 0 seconds
// concat2.next 1 seconds
// concat2.next 2 seconds
// concat2.next 0 half seconds
// concat2.next 1 half seconds
// concat2.next 2 half seconds
// concat2.complete
```

### zip, combineLatest, withLatestFrom
- ```combineLatest```: Observable 각각의 데이터가 emit될 때, 각 Observable의 최근 data를 서로 combine 한다.
- ```zip```: 모든 Observable이 complete된 후에, 배열로 데이터를 emit한다.

```javascript
const currentUser$ = new Rx.BehaviorSubject({ isLoggedIn: false});

Rx.Observable.interval(1000) // event click
    .withLatestFrom(currentUser$)
    .filter([i, user] => user.isLoggedIn)    // withLatestFrom은 array를 리턴한다.
    .subscribe(createSubscriber('withLatestFrom'));

// noting print

setTimeout(() => {
    currentUser$.next({ isLoggedIn: true });
}, 3000);

// output 
//
// withLatestFrom.next 1,[object Object]
// withLatestFrom.next 2,[object Object]
// withLatestFrom.next 3,[object Object]
// withLatestFrom.next 4,[object Object]
// ...
```

***

## Transformation

### mergeMap(flatmap)

주로 ```map```과 ```mergeAll``` operation을 한번에 수행하고 싶을 때 사용한다.

```javascript
Rx.Observable.range(2, 3)
    .mergeMap(i => Rx.Observable.time(i * 2000)
    .map(() => `After ${i * 2} Seconds`))
    .subscribe(createSubscriber('mergeMap'));
    
// output
//
// mergeMap.next After 4 seconds
// mergeMap.next After 6 seconds
// mergeMap.next After 8 seconds
// mergeMap.complete
```

```javascript
function query(value) {
    return new Promise((resolve, reject) => {
    	setTimeout(() => {
        	resolve('this is the value');
        }, 1000);
    });
}

Rx.Observable.of('my query')
    .do(() => console.log('Querying'))
    .mergeMap(a => query(a))
    .do(() => console.log('After Querying'))
    .subscribe(createSubscriber('mergeMap'));

// output
//
// Querying
// After Querying
// query.next this is the value
// query.complete
```

### switchMap

이전의 Observable이 complete된 후에 새로운 값을 emit한다.
기존의 old observable 데이터를 신경쓰지 않거나 여러 서비스를 차례로 호출해야 할 때, ```switchMap```을 사용한다.
 
```javascript
Rx.Observable.of('some_url')
    .switchMap(url => this.http.get(url).map(res=>res.json())
    .switchMap(res => this.http.get(url + 'res').map(res=>res.json())
```

### reduce

하나의 single value로 만든다.

```javascript
Rx.Observable.range(1, 10)
    .reduce((acc, value) => acc + value)
    .subscribe(createSubscriber('reduce'));
    
// output
//
// reduce.next 55
// reduce.complete
```

### scan

각 데이터에 ```scan``` 함수를 적용하고 emit한다. 데이터가 들어올 때마다 값을 처리하는 데 매우 좋다.

```javascript
Rx.Observable.range(1, 10)
    .map(i => i * i)
    .scan(([last, _], current) => [current, last], [])
    .subscribe(createSubscriber('scan'));
    
// output
//
// scan.next 1
// scan.next 4,1
// scan.next 9,4
// scan.next 16,9
// scan.next 25,16
// scan.next 36,25
// scan.next 49,36
// scan.next 64,49
// scan.next 81,64
// scan.next 100,81
// scan.complete
```

### bufferCount

```javascript
Rx.Observable.range(1, 100)
	.bufferCount(50)
	.subscribe(createSubscriber('bufferCount'));
    

// output
//
// bufferCount.next 1,2,3,4,...25
// bufferCount.next 26,27,28,...50
// bufferCount.next 51,52,53,...75
// bufferCount.next 76,77,78,...100
// bufferCount.complete
```

### bufferTime

```javascript
Rx.Observable.interval(500)
	.bufferTime(2000)
	.subscribe(createSubscriber('bufferTime'));
    

// output
// 2초마다 아래 next 발생
// bufferTime.next 0,1,2
// bufferTime.next 3,4,5,6,7
// bufferTime.next 8,9,10,11
```

### buffer

```javascript
Rx.Observable.interval(500)
	.buffer(Rx.Observable.interval(2000))
	.subscribe(createSubscriber('buffer'));
    

// output
// 2초마다 아래 next 발생
// buffer.next 0,1,2
// buffer.next 3,4,5,6,7
// buffer.next 8,9,10,11
```

### toArray
Observable이 complete될 때까지, source 스트림의 데이터를 collect한다. 

```javascript
Rx.Observable.range(1, 7)
    .toArray()
    .subscribe(createSubscriber('toArray'));
    
// output
//
// toArray.next 1,2,3,4,5,6,7
// toArray.complete 
```

