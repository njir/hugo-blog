# Glossary
mergeMap for async or concatMap for sync

reactive thinking: 내 데이터는 어디에서 오는가

## Observables
계속해서 data를 생성(emit, produce)하는 일종의 collection 

## Operators
Observable로부터 나온 data들을 수정하거나 처리하기 위한 wrapper (ex. filter, take, map...)

## Subscriptions
Observable과 Operator로부터 처리된 데이터를 실제로 다루는 장치

***

# Observable code-snippet
아래 작성된 코드들은 rxjs ver5를 기준으로 한다.

## Createing Observable

```
import Rx from 'rxjs/Rx';

// 여기서 변수 뒤의 $표시는 해당 변수가 observable이라는 뜻으로 관용적으로 쓰인다.
const simple$ = new Rx.Observable(observer => {
	console.log('generating obserbable');
    setTimeout(() => {
    	observer.next('an item!');
        setTimeout(() => {
        	observer.next('another item!');
            observer.complete();
        }, 1000);
    }, 1000);
});

```

Observable을 만들어보자. 위 코드까지만 작성 후, 실행하면 아무 일도 발생하지 않는다. <br>중요한 점은 Observable은 **lazy**한 특성을 갖고 있다. 즉, subscription을 갖기 전까진 observable은 실행되지 않는다. <br> 이제 subscription을 작성해보자.


## Subscribe

```
...

simple$.subscribe(
	item => console.log(`one.next ${item}`), //next
    error => console.log(`one.error ${error}`),	// error
    () => console.log('one.complete'));	// complete
    
// output
// 
// Generating observable
// one.next an item!
// one.next another item!
// one.complete

```

simple$에 subscribe를 등록하여 해당 observable이 실행되게 만들었다. <br>subscribe의 args은 3가지다. 첫번째는 next callback, 두번째는 error callback, 세번째는 complete callback이다. observable에 data가 남아있을 경우, 계속 next가 실행되며 data가 남아있지 않을 때 complete이 실행된다. <br>complete은 observable의 모든 data를 다 처리했다는 일종의 시그널이다. 


```
...

setTimeout(() => {
	simple$.subscribe({
    	next: item => console.log('two next ${item}'),
        error(error) {
        	console.log('two.error ${error}')
        },
        complete: function() {
        	console.log('two.complete');
        }
    });
}, 3000);

// output
// 
// Generating observable
// one.next an item!
// one.next another item!
// one.complete
// Generating observable 
// two.next an item!
// two.next another item!
// two.complete
```

이미 complete 된 observable에 다시 subscribe를 하게 되면 또 다시 data를 처리할 수 있다. 위 코드 블럭에서 두 번째 ```Generating observable```처럼 다시 observable이 생성되는 게 중요하다.


## Error

observable에 error를 emit하려면 아래 코드처럼 작성하면 된다. 

```
import Rx from 'rxjs/Rx';

const error$ = new Rx.Observable(observer => {
	observer.error(new Error('whoa'))
});

error$.subscribe(
	item => console.log('one.next ${item}), //next
    error => console.log('one.error ${error.stack}), // errpr
    () => console.log('one.complete'));	// complete
   
// output
// 
// one.error Error: whoa
```

## Creating take$ operator

observable에서 첫번째부터 n개까지의 data를 emit하는 take$ operator를 작성해보자.

```
function createInterval$(time) {
	return new Rx.Observable(observer => {
    	let index = 0;
        setInterval(() => {
        	observer.next(index++);
        }, time);
    });
}

function createSubscriber(tag) {
	return {
    	next(item) {
        	console.log(`${tag}.next ${item}`); 
        },
        error(error) {
        	console.log(`${tag}.error ${error.stack || error}`); 
        },
        complete() {
        	console.log(`${tag}.complete`); 
        },
    };
}
       
// creating 'take' operator
function take$(sourceObservable$, amount) {
	return new Rx.Observable(observer => {
    	let count = 0;
        const subscription = observer.subscribe({
        	next(item) {
            // 이 부분을 수정하면 filter, take.. 등을 구현할 수 있다.
            	observer.next(item);
                if (++count >= amount)
                	observer.complete();
            },
            error(error) { observer.error(error); },
            complete() { observer.complte(); }
        });
        
        return () => subscription.unscribe();
    });
}

const everySecond$ = createInterval$(1000);
const firstFiveSecond$ = take$(everySecond$, 5);
const subscription = firstFiveSecond$.subscribe(createSubscriber('one'));

// output. 1초마다 한번씩 찍힌다.
//
// one.next 0
// one.next 1
// one.next 2
// one.next 3
// one.next 4
// one.complete
```

```creatingInteval$``` 함수는 주어진 시간마다 observable의 next를 emit하는 역할을 한다. ```createSubscriber``` 함수는 observable을 subscribe하는 함수로, 단순히 console.log를 찍게 된다.


# Operators Built in Observable

Observable를 만들 수 있는 operator에 대해 알아보자. 간단히 얘기하자면 ```operator```는 observable의 **wrapper**에 불과하다


## Creating Observables

### interval
```interval```은 시간 간격을 지정하고 data를 emit한다. 아래 코드는 500ms의 인터벌을 가지면서, ```take``` operator로 data를 3개까지만 처리한다. 

```
import Rx from 'rxjs/Rx';

function createSubscriber(tag) {
	return {
    	next(item) { console.log(`${tag}.next ${item}`); },
        error(error) { console.log(`${tag}.error ${error.stack || error}`); },
        complete() { console.log(`${tag}.complete`); },
    };
}

Rx.Observable.interval(500)
	.take(3)
    .subscribe(createSubscriber('interval'));

// output
//
// one.interval 0
// one.interval 1	
// one.interval 2
// one.complete
```

### timer
```interval```과는 달리, 주어진 시간이 지나면 data를 emit하는 Observable을 만든다.

```
Rx.Observable.timer(5000)
    .subscribe(createSubscriber('timer'));

// output
// 5초 후에 아래 console.log가 찍힌다.
// timer.next 0
// timer.complete


Rx.Observable.timer(1000, 500) // 1초 후에 500ms마다 데이터 처리
    .subscribe(createSubscriber('timer'));
```

### of, from

```of```는 주어진 arg를 Observable sequence로 바꾼다. 참고로 ```of``` 안에 array가 들어가면 안된다. array 자체를 하나의 observable로 인식해버린다. array의 데이터들을 sequence로 바꾸려면 ```from```을 쓰자.

```
Rx.Observable.of('HELLO, WORLD', 42, 'whoa')
    .subscribe(createSubscriber('of'));
    
// output
//
// of.next HELLO, WORLD
// of.next 42
// of.next whoa
// of.complete
```

```
Rx.Observable.from([23, 10, 4, 'hey'])
    .subscribe(createSubscriber('from'));
    
// output
//
// from.next 23
// from.next 10
// from.next 4
// from.complete
```

### throw 

```throw```는 observable의 error를 emit하기 위해 쓰인다.

```
Rx.Observable.throw(new Error('hey'))
    .subscribe(createSubscriber('error'));

// output
// 
// error.error hey
```

### empty, never
```empty```는 complete만 emit하며, ```never```는 아무런 데이터도 generate하지 않는다.
	
```
Rx.Observable.empty()
    .subscribe(createSubscriber('empty'));
    
// output
//
// empty.complete


Rx.Observable.never()
    .subscribe(createSubscriber('never'));
    
// output
// 
// 없음.
```

### range
일정 범위의 sequence한 정수를 emit 한다.

```	
Rx.Observable.range(1, 4)
    .subscribe(createSubscriber('range'));
    
// output
// 
// range.next 1
// range.next 2
// range.next 3
// range.complete 
```

### bindNodeCallback
Node.js 스타일의 callback API를 Observable을 반환한다. 자세한건 코드를 참고하자.

```
import Rx from 'rxjs/Rx';
import { createSubscriber } from './lib/util';
import fs from 'fs';

fs.readdir('./src-server', (err, items) => {
	if (err) console.log(err);
    else console.log(items);
}); // output: example1.js, example2.js ...

const readdir$ = Rx.Observable.bindNodeCallback(fs.readdir);
console.log(typeof (readdir$));	// function
// 여기서 readdir 타입이 observable이 아니라 function이라는 점이다.

readdir$('./src-server')
	.mergeMap(files => Rx.Observable.from(files))
    .map(fileName => `Manipulate ${fileName}`)
	.subscribe(createSubscriber('readdir'));
    
//output
//
// readdir.next example1.js
// readdir.next example2.js
// readdir.next example3.js
// readdir.complete
```

### fromPromise
```bindNodeCallback```과 마찬가지로 Promise로부터 Observable을 만든다.

```
function getItem() {
	return new Promise((resolve, reject) => {
    	setTimeout(() => {
        	resolve('hello');
        }, 1000);
    });
}

Rx.Observable.fromPromise(getItem())
	.subscribe(createSubscriber('promise'));
    
// output
// 
// promise.next hello
// pormise.complete

```

# Subject code-snippet

observer와 Observable을 둘 다 상속 받는 객체
<br> reactive 코드와 그렇지 않은 코드를 연결할 때 사용될 수 있다. 

```
import Rx from 'rxjs/Rx';
import { createSubcriber } from './util';

const simple$ = new Rx.Subject();

simple$.suscribe(createSubscriber('simple$'));

simple$.next('hello');
simple$.next('world');
simple$.complete();

// output
// 
// simple$.next hello
// simple$.next world
// simple$.complete
```

Subject는 observer이면서 observable이기 때문에 subscribe, next, complete을 직접할 수 있다.

```
const interval$ = Rx.Observable.interval(1000).take(3);
const intervalSubject$ = new Rx.Subject();

interval$.subscribe(intervalSubject$);

intervalSubject.subscribe(createSubscriber('sub1'));
intervalSubject.subscribe(createSubscriber('sub2'));

// output
//
// sub1.next 0
// sub2.next 0
// sub1.next 1
// sub2.next 1
// sub1.next 2
// sub2.next 2
// sub1.complete
// sub2.complete
```

```
const currentUser$ = new Rx.Subject();
const isLoggedIn$ = currentUser$.map(u => u.isLoggedIn);

isLoggedIn$.subscribe(createSubscriber('isLoggedIn;));

currentUser$.next({ isLoggedIn: false });
setTimeout(() => {
	currentUser$.next({ isLoggedIn: true, name: 'tak' });
}, 2000);

// output
//
// isLoggedIn.next false
// isLoggedIn.next true
```

모든 구독자들에게 알려줄 수 있다. (notify)
subject 종류는 behaviorsubject, replaysubject...

```
const replay$ = new Rx.ReplaySubject(3);
replay$.next(1);
replay$.next(2);

replay$.subscribe(createSubscriber('one'));

replay$.next(3);
replay$.next(4);
replay$.next(5);

replay$.subscribe(createSubscriber('two'));

replay$.next(6);

// output
//
// one.next 1
// one.next 2
// one.next 3
// one.next 4
// one.next 5
// two.next 3
// two.next 4
// two.next 5
// one.next 6
// two.next 6

```

asyncsubject는 오직 complete이 발생한 후에 emit 된다.

```
const apiCall$ = new Rx.AsyncSubject());
apiCall.next(1);
apiCall.next(2);
apiCall.complete();
```


***

## Rxjs Resources
- doc문서는 https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api 를 보자
- 대신 이 폼페이지는 v4 이기 때문에 rename되거나 deprecated된 operator가 있을 수 있다.
- https://github.com/ReactiveX/rxjs/blob/master/MIGRATION.md 를 챰고하자ㅏ
- rxmarble
- rxvision playground
- jaredforsyth.com/rxvision/examples/playground


## Hot vs Cold Observable

```
Rx.Observable.fromEvent($title, 'keyup')
// this is hot observable
// because the key then is going to be fired regardless of whether or not you subscribe to it.
// so the expectation here with a hot observable is that you do not expect to receive historical data.
// you only expect to receive data the moment you plug in to that stream.

// we have no expectation of receiving every single historic key up and that's what you should be thinking
```

```
import Rx from 'rxjs/Rx';

const simple$ = new Rx.Observable(observer => {
	console.log('generating observable');
    setTimeout(() => {
    	observer.next('an item!');
        setTimeout(() => {
        	observer.next('another item!');
            observer.complete();
        }, 1000);
    }, 1000);
});

// when you subscribe to it, that's when it starts doing something and you expect to receive every single item from the start.
// from start to finish data
```

missing a value를 기대한다 -> cold observable




### Hot observable

```
import Rx from 'rxjs/Rx';
import { createSubscriber } from './lib/util';

const interval$ = Rx.Observable.interval(1000)
					.take(10);

setTimeout(() => {
	interval$.subscribe(createSubscriber('one'));
}, 1200);

setTimeout(() => {
	interval$.subscribe(createSubscriber('one'));
}, 3200);

// output
//
// one.next 0
// one.next 1
// two.next 0 // 3초 후 다시 새롭게 시작함 -> 즉 cold observable임
// one.next 2

```

하지만 얘를 다시 hot observable로

```
const interval$ = Rx.Observable.interval(1000)
					.take(10)
                   	.publish();		// connect() 하기 전엔 실행되지 않는다.
                    
interval$.connect();

setTimeout(() => {
	interval$.subscribe(createSubscriber('one'));
}, 1200);

setTimeout(() => {
	interval$.subscribe(createSubscriber('one'));
}, 3200);

// output
//
// one.next 1
// one.next 2
// one.next 3
// two.next 3 <- 이부분
// one.next 4
// two.next 4

```

connect 하기 전까진 실행되지 않는다. 매우 중요
it only starts executing when Connect is called.



```
const chatMessages$ = new Rx.Observable(observer => {
	socket.on('chat: message', message => observer.next(message));
});
// 여기까지는 cold observable이다.


const chatMessages$ = new Rx.Observable(observer => {
	socket.on('chat: message', message => observer.next(message));
}).publish();

chatMessages$.connect();

chatMessages$.subscribe(createSubscriber('chat'));

```

hot observable이 중요한 점은 해당 옵저버블이 한번만 실행되고 같은 정보를 공유하게 된다는거다.

```publishLast()```의 경우, observable이 complete가 되면 그때서야 실행된다. last value

```refCount()```
connect된 observable 관리할 때 쓴다. 
disconnecting from it when it's done ref count will connect to the observable on the first subscription

it's not going to re-run this function because we connected another subscriber

big long operation에 쓰인다. 10million 등



***

## do / finally / startWith / filter

** 중요 **
every operator create a new observable

### do / finally

**매번** 실행 된다. 새로운 아이템이 emit 될때마다
From do 1, 2, 3, 데이터와 map의 데이터가 서로 다르다.

```
Rx.Observable.range(1, 5)
	.do(a => console.log('From do ${a}`))
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

Rx.Observable.range(1, 5)
	.finally(a => console.log('From finally`))
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

**finally** only executes after it gets completed after the subscription is completed 

### filter

```
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

### startWith
```
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

두가지 방법이 있다.

one sequence into another


```
Rx.Observable.interval(1000)
	.merge(Rx.Observable.interval(500))
    .take(5)
    .subscribe(createSubscriber('merge1'));

Rx.Observable.merge(
	Rx.Observable.interval(1000).map(i => `${i} secondes`),
	Rx.Observable.interval(500).map(i => `${i} half secondes`))
    .take(8)
    .subscribe(createSubscriber('merge2'));
    

//output
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

### concat
```
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

#### concat vs merge 
```
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

### MergeMap(flatmap)

return another projection
to perform both a map operation and merge operation at once

```
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

```
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

### SwitchMap
forget old value (from old observable)






### Reduce
into single value

```javascript
Rx.Observable.range(1, 10)
	.reduce((acc, value) => acc + value)
	.subscribe(createSubscriber('reduce'));
    
// output
//
// reduce.next 55
// reduce.complete
```

### Scan
awesome at bringing in valuse or processing values as data came in, 

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


function scanLast(acc, value) {
	const last = acc[0];
    return [valuse, last];
}
```

### Buffer

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

Rx.Observable.interval(500)
	.bufferTime(2000)
	.subscribe(createSubscriber('bufferTime'));
    

// output
// 2초마다 아래 next 발생
// bufferTime.next 0,1,2
// bufferTime.next 3,4,5,6,7
// bufferTime.next 8,9,10,11

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
collect every single items from source stream until source terminated

```javascript
Rx.Observable.range(1, 7)
    .toArray()
    .subscribe(createSubscriber('toArray'));
    
// output
//
// toArray.next 1,2,3,4,5,6,7
// toArray.complete
    
```

### first, last, single

각 operator에서 알아야할 점은 next()가 없으면 ```EmptyError```가 발생한다.

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

```single``` operator는 next를 한번만 emit해야 한다. 그렇지 않으면 에러가 발생

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

take: observable에서 ```take```의 인자 만큼 data를 처리한다.
skip: 말그대로 data를 skip한 후 나머지 데이터만 처리

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

### takeUntil
skipUntil, takeUntil 은 Observable, Subject 데이터를 인자로 갖는다.

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

### zip, combineLatest, withLatestFrom

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

# Error Handling
observable은 한번 실패하면 그 상태로 done이다. 더 이상 데이터를 emit하지 않고 complete 하지 않는다.
## catch , retry가 있다.

