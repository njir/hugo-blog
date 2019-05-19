+++
title = "자바스크립트 Scope의 이해"
description = "자바스크립트의 Scope와 Hoisting 정리"
tags = [
    "til",
    "javascript"
]
date = "2019-05-19"
menu = "main"
+++

# Javascript Scope의 이해

아래 코드를 실행하면 ```console.log(text)```은 어떻게 찍힐까?

```javascript
var text = 'outside';
function logIt() {
    console.log(text);
    var text = 'inside';
};
logIt();
```

답은 ```undefined```다.

<br />
## 1. Funcion-Level Scope

자바스크립트는 function-level scope를 갖고 있다. 자바스크립트에서 함수가 실행될 때 새로운 스코프를 만든다. 아래 코드를 참조하자. 참고로 ```Reference Error(not defined)```와 ```undefined```는 다르다.

```javascript
function setName() {
	// 아래 변수는 외부에서 접근할 수 없다.
	// 오직 setName() 함수 안에서만 접근 가능하다
	var name = 'tak';
}
setName();
console.log(name);  // throws 'ReferenceError: name is not defined'
```

C++, Java와 달리 자바스크립트는 block 레벨에서 스코프를 생성하지 않는다. (**var 선언의 경우에만**)

```javascript
if (true) {
    // if문 안에서는 새로운 스코프를 생성하지 않는다.
    // name 변수는 전역에서 접근 가능하다.
    var name = 'tak';
}
console.log(name);  // 'tak'
```

<br />
## 2. Declaration vs Assignment

자바스크립트에서 변수를 선언(declaration)하게 되면 인터프리터는 기본적으로 ```undefined```로 초기화한다. 한번 선언된 변수는 Reference Error가 발생하지 않는다.

```javascript
var unicorn;
console.log(unicorn);  // logs undefined (NOT a ReferenceError)
```

변수 할당(assignment)은 아래 코드처럼 말 그대로 변수에 값을 할당하는 것이다.

```javascript
unicorn = 'Sparkles';
```

즉, 아래 코드는 변수 선언과 할당을 동시에 한다.

```javascript
var unicorn = 'Sparkles';
```

<br />
## 3. Execution Context

자바스크립트의 실행 컨텍스트. C++이나 자바의 스택영역과 유사하다. 자바스크립트에서 실행되고 있는 코드를 관리해주는 일종의 wrapper다. 함수가 실행되는 환경이라고 생각하자.
자바스크립트에서는 Globl Execution Context가 기본적으로 돌아가고 있다. 개발자가 정의한 함수가 실행될 때, 새로운 Execution Context가 생성되어 스택 구조로 순차적으로 실행된다.

<br />
## 4. Hoisting

위의 개념을 토대로 Hoisting을 이해할 수 있다. Hoisting이란 변수를 선언했을 때 해당 변수가 현재 스코프의 최상단에 끌어올려지는 개념이다. Execution Context는 생성과 실행이라는 2단계를 거치는데, 생성 단계에서 변수와 함수를 위한 메모리 공간을 확보하게 된다. 이 과정에서 호이스팅이 발생한다.

즉, 문제의 코드가 실행될 때 초기 생성 단계에서는 아래 코드와 같다.

```javascript
var text = 'outside';
function logIt(){
    var text;
    console.log(text);
    text = 'inside';
};
logIt();
```

따라서 ```console.log(text)```의 결과는 text 변수에 아무 값도 할당되지 않은 ```undefined```를 출력하게 된다.

자바스크립트에서 var 변수를 선언할 때는 항상 **현재 스코프**의 최상단으로 호이스팅된다는 것을 기억하자. ES6 환경에서는 ```let```, ```const``` 변수 쓰는 게 마음 편하다.