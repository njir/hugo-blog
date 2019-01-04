+++
title = "Advanced Java04 - Java Object"
description = "온라인 T아카데미 강의 Advanced Java 정리"
tags = [
    "java",
]
date = "2015-03-07"
menu = "main"
+++


#### Interface
- class정의시 "어떤 정보가 어떻게 되면 외부에 알려준다."와 같은 경우에 사용
- 예를 들어 함수 정의시 변수가 10의 배수가 되면 외부에 알려주고 싶을 때 사용

```java
public interface CounterListener{
	public voic onNotifyCount(int count);
}
public class Counter {
	private int mCount=0;
	CounterListener mListener;
	public void setCounterListener(CounterListener listener){
		mLisener = listener;
	}
public void inc(){
	mCount++;
	if(mCount % 10 ==0){
		//notify out side
		if(mListener != null){
				mListener.onNotifyCount(mCount)
			}
		}
	}
}
```
- 공통적으로 사용되는 속성을 정의하고자 할 때
- 객체간 값을 비교하는 속성을 정의하고자 하는 경우 해당 속성을 interface로 정의하고 class에서 이 interface를 implements하게 한 다음, 해당 class의 객체를 interface type으로 변환하여 사용

#### inner 클래스
- 클래스 안에서 클래스를 정의하는 것
- When? 클래스 내부에서만 사용되는 클래스를 정의할 때

```java
public class OutterClass{
	public void printMessage(OutterClass obj){..}
	public class InnerClass{
		public voide invokeOutter(){
			printMessage(OuterClass.this); 
			// outter 클래스의 객체가 생성되어 있을 때만 inner 클래스의 객체 생성 가능 
			// static으로 정의되면 따로 생성 가능
		}
	}
}
```

#### Anonymous 클래스
- 이름 없이 class를 정의하여 객체를 생성한 class

#### The instanceof Operator
- 객체의 타입을 알아보는 연산자

```java
public class Employee extends Object
public class Manager extends Employee
public class Engineer extends Employee	
public void doSomething(Employee e){	// 타입이 super에 해당해야함
	if(e instanceof Manager){
    	// Manager
	} else if(e instanceof Engineer){
		// Engineer
	} else {
		..
	}
}
```

#### Cating Object
- 객체를 캐스팅함으로써 그 객체의 모든 멤버에 접근할 수 있다.
- (class의 type)객체

#### Object Class
- Object 클래스는 모든 클래스의 최상위 클래스
- 즉, 자바에서의 모든 클래스의 super class

#### The '==' Operator Compared With the equals Method
- '= =' 연산자는 두 개의 레퍼런스가 같은지를 판단한다.(그 객체가 같은지) 같은 객체인지를 판단
- 'equals()'는 안에 있는 내용이 같은지를 판단
```java
String a = new String("test");
String b = a;
if (a == b) {
	// true
}


String a = new String("test");
String b = new String("test");
// a==b 아님
if(a.equals(b)){
	//true
}
```

#### The toString Method
- 자바의 모든 객체는 string 연산 가능
- 객체를 String으로 바꿀 때 사용

#### Wrapper Classes
- Primitive 타입(int, char, byte...)을 오브젝트처럼 다룰 수 있다.
- 자바는 Primitive 타입에 해당하는 각 오브젝트 클래스를 정의하고 있다.

Primitive Type     | Wrapper Class
boolean 		   | Boolean
byte    		   | Byte
char     | Characater
... | ...

#### Obect Runtime 구조(자바 VM)
- 자바는 스택이 있고 스택 안에 Local Variable Array가 있다. (int i, j=30 ...) 변수들이 있는 공간
- HEAP : new로 생성할 때, new로 만든 객체가 들어감
- CLASS AREA : 내가 만든 class , 코드가 메모리에 올라가는 곳
- 바이트 코드 : stack 기반의 명령어 + 객체지향 명령어로 구성