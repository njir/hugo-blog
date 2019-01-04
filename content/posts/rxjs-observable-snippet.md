+++
title = "RxJS Observable 간단정리"
description = "RxJS ver5을 기준으로 간단한 observable, operator 정리 및 관련된 code snippet 모음"
tags = [
    "rxjs",
    "Reactive",
    "javascript"
]
date = "2018-02-15"
menu = "main"
+++


시작하기 전에, Reactive Programming은 **내가 원하는 데이터는 어디에서 오는가**를 항상 생각해야 한다고 한다.

# Glossary

## Observables
계속해서 data를 생성(emit, produce)하는 일종의 collection 

## Operators
Observable로부터 나온 data들을 수정하거나 처리하기 위한 wrapper (ex. filter, take, map...)

## Subscriptions
Observable과 Operator로부터 처리된 데이터를 실제로 다루는 장치

***

# Observable 
아래 작성된 코드들은 rxjs ver5를 기준으로 한다.

## Creating Observable

```javascript
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

Observable을 만들어보자. 위 코드까지만 작성 후, 실행하면 아무 일도 발생하지 않는다.<br>
중요한 점은 Observable은 **lazy**한 특성을 갖고 있다.
즉, subscription을 갖기 전까진 observable은 실행되지 않는다.
<br>이제 subscription을 작성해보자.

## Subscribe

```javascript
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

simple$에 subscribe를 등록하여 해당 observable이 실행되도록 만들었다.<br>
subscribe의 args은 3가지다. <br>
첫번째는 next callback, 두번째는 error callback, 세번째는 complete callback이다. 
Observable에 data가 남아있을 경우, 계속 next가 실행되며 data가 남아있지 않을 때 complete이 실행된다.
<br>complete은 observable의 모든 data를 다 처리했다는 일종의 시그널이다. 


```javascript
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

이미 complete 된 observable에 다시 subscribe를 하게 되면 또 다시 data를 처리할 수 있다.<br>
위 코드 블럭에서 두 번째 ```Generating observable```처럼 다시 observable이 생성되는 게 중요하다.


## Error

observable에 error를 emit하려면 아래 코드처럼 작성하면 된다. 

```javascript
import Rx from 'rxjs/Rx';

const error$ = new Rx.Observable(observer => {
	observer.error(new Error('whoa'))
});

error$.subscribe(
    item => console.log(`one.next ${item}`), //next
    error => console.log(`one.error ${error.stack}`), // error
    () => console.log(`one.complete`));	// complete
   
// output
// 
// one.error Error: whoa
```

## Creating take$ operator
대충 개념은 익혔으니, observable에서 첫번째부터 n개까지의 data를 emit하는 take$ operator를 작성해보자.

```javascript
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

***

# Operators 

## Creating observable with operator

Observable를 만들 수 있는 operator에 대해 알아보자. 간단히 얘기하자면 ```operator```는 observable의 **wrapper**에 불과하다

### interval
```interval```은 시간 간격을 지정하고 data를 emit한다. 아래 코드는 500ms의 인터벌을 가지면서, ```take``` operator로 data를 3개까지만 처리한다. 

```javascript
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

```javascript
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

```of```는 주어진 arg를 Observable sequence로 바꾼다. <br>
참고로 ```of``` 안에 array가 들어가면 안된다. array 자체를 하나의 observable로 인식해버린다. array의 데이터들을 sequence로 바꾸려면 ```from```을 쓰자.

```javascript
// of operator
Rx.Observable.of('HELLO, WORLD', 42, 'whoa')
    .subscribe(createSubscriber('of'));
    
// output
//
// of.next HELLO, WORLD
// of.next 42
// of.next whoa
// of.complete
```

```javascript
// from operator
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

```javascript
Rx.Observable.throw(new Error('hey'))
    .subscribe(createSubscriber('error'));

// output
// 
// error.error hey
```

### empty, never
```empty```는 complete만 emit하며, ```never```는 아무런 데이터도 generate하지 않는다.
	
```javascript
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

```javascript
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

```javascript
import Rx from 'rxjs/Rx';
import { createSubscriber } from './lib/util';
import fs from 'fs';

fs.readdir('./src-server', (err, items) => {
    if (err) console.log(err);
    else console.log(items);
}); // output: example1.js, example2.js ...

const readdir$ = Rx.Observable.bindNodeCallback(fs.readdir);
console.log(typeof (readdir$));	// function
// 여기서 readdir 타입은 observable이 아니라 function이다.

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

```javascript
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

*** 

# Subject 

```Subject```는 observer와 Observable을 둘 다 상속 받는 객체
<br> reactive 코드와 그렇지 않은 코드를 연결할 때 사용될 수 있다고 하는데, 무슨 말인지 아직 정확히 이해를 못했다.
<br> Subject를 통해 subject를 구독하는 모든 observer들에게 알려줄 수 있다. (notify 역할)

```javascript
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

```javascript
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

```javascript
const currentUser$ = new Rx.Subject();
const isLoggedIn$ = currentUser$.map(u => u.isLoggedIn);

isLoggedIn$.subscribe(createSubscriber(`isLoggedIn`));

currentUser$.next({ isLoggedIn: false });
setTimeout(() => {
    currentUser$.next({ isLoggedIn: true, name: `tak` });
}, 2000);

// output
//
// isLoggedIn.next false
// isLoggedIn.next true
```

## Subject의 종류


### BehaviorSubject

Observable의 마지막 data만을 받는다.

### AsyncSubject

Asyncsubject는 오직 complete이 발생한 후에 emit 된다. 그리고 Observable로부터 배출된 마지막 data를 배출한다.

### ReplaySubject

```replay subject```는 opserver가 구독을 시작한 시점과 관계 없이, observable들이 emit한 모든 항목들을 모든 observer에게 배출한다.<br>

```javascript
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

