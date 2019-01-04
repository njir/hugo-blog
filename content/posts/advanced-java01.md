+++
title = "Advanced Java01 - 객체지향, 기본 구조, Keyword"
description = "온라인 T아카데미 강의 'Advanced Java 정리' 객체지향, 기본 구조"
tags = [
    "java",
]
date = "2015-03-07"
menu = "main"
+++

# 객체지향

## 컴퓨터 프로그래밍
하나 이상의 관련된 추상 알고리즘을 특정한 프로그래밍 언어를 이용해 구체적인 컴퓨터 프로그램으로 구현하는 기술


## 추상화
복잡한 자료, 모듈, 시스템 등으로부터 핵심적인 개념, 기능을 간추려 내는 것을 말한다. 즉, 컴퓨터가 이해할 수 있도록 개념적으로 만들어 내는 작업.

> **Note**

> Control Abstraction : 처리 작업에 대한 추상화. 
> ex) 컵에 물을 따라 마시는 과정
<br>
> Abstraction Data Type : 어떠한 데이터를 갖고 있느냐에 초점.
> ex) 컵에 대한 속성을 지정 

## 절차지향 vs 객체지향 
절차지향: 어떤 절차를 따라 처리를 해서 결과를 내면 된다. 문제를 해결하기 위한 과정을 기술 **(컴퓨터는 기본적으로 절차지향)**

```java
// 도형의 넓이 구하기 - 절차지향
struct {
	int type;
	point * points;
}Shape;

Shape s[100];
int sum=0;
for(int i=0; i<100; i++) {
	switch(s[i].type){
		case TRIANGLE:
			sum+=..
		case RECTANGLE:
			sum+=..
		..
	}
}
print sum;
```

객체지향: 어떤 문제를 해결하기 위해 문제를 구성하고 있는 각 개체를 정의하고 관계를 기술해 문제를 해결. 속성과 관계에 대한 추상화

```java
// 도형의 넓이 구하기 - 객체지향
abstract class Shape {	//현실은 도형이 없으니까 추상화
	abstract class float calArea();
}

class Triangle extends Shape {
	float calArea() {..}
}

class Rectangle extends Shape {
	float calArea() {..}
}

class Circle extends Shape {
	float calArea() {..}
}

Shape s[100];
int sum=0;
for (int i=0; i<100; i++) {
	sum+=s[i].calArea();
	...
}
```
> **Tip:** 이 둘은 기호가 아님. 무엇을 프로그래밍 할거냐, 어떤 문제를 해결하느냐가 중요 문제

## 객체지향 특성
### Abstraction
 - 객체와 프로시저들의 공통의 특징들을 골라내는 과정
 - ex) 도형의 넓이를 계산하는 Method(behaviors), 도형의 포인트라는 Attribute를 갖는 Shape(객체)

### Information Hiding 
 -  객체의 데이터를 외부에서 접근 불가능(why? 잘못된 data설정 가능)
 -  주로 값에 제한사항이 있을 때 사용
 -  limitation 없을 땐 굳이 사용 안해도 됨

### Encapsulation
 - 구현된 클래스의 세부사항(Attribute, method 등)을 숨김
 - 장점 : 클래스 내부가 어떻게 되어있는지 몰라도 됨. 단순히 쓰기만 하면 됨. 프로그래밍 쉬워짐
 - 단점 : 그 기능을 변경하고자할 때 변경하기 어려움(확장에 제약). 기껏 숨겨놨더니 확장하려니까 다시 세부사항까지 알아야 함

## Object vs Class
- Object: 오브젝트는 메모리에 생성됨
- Class: 클래스는 메모리에 로딩(정확하게는 생성하지만 일반적으로 로딩한다고 말함)

> **Note :**
> - 사물에 대한 설계도가 클래스
> - 설계도를 이용해 만든 사물이 오브젝트
> - 만들어진 사물이 있는 곳 : 메모리(힙)
> - ex) 컴퓨터 -> 내가 쓰는 컴퓨터, 니가 쓰는 컴퓨터 서로 다름

한마디로 Class as Blueprints for Objects

## 객체간 관계
Is-a vs Has-a: 객체는 관계속에서 움직인다.

### Is-a: Triangle은 shape 이다, drawable이다.

```java
Triangle t = new Triangle();
Shape s = (Shape)t;
Drawable d = (Drawable)t;
```

### Has-a  

```java
Triangle t = new Triangle();
Point p = t.points;
```

## 객체지향 특징
### Inheritance
- 상속은 새로운 클래스가 기존의 클래스의 자료와 연산을 이용할 수 있게 하는 기능
- 슈퍼클래스의 기능을 이어 받고 달라진 기능만 새로 설계한 클래스 => 서브클래스
- 상속을 통해 기존의 클래스를 하위 클래스를 이용해 프로그램의 요구에 맞추어 클래스를 수정하고 클래스 간의 종속 관계를 형성 가능
> 기반, 상위. 부모 클래스 vs 파생, 하위, 자식 클래스

###  Polymorphism 
- 다형성은 많은 다른 형태를 가질 수 있는 성질을 말한다.
- 하나의 객체는 오직 하나의 형태만을 가진다.
- 레퍼런스 변수는 다른 여러 형식의 객체를 가리킬수 있음.

### Overriding Method
- 상위 클래스로부터 상속 받은 메소드를 하위 클래스를 수정할 수 있다.
- 같은 메소드인데, 어떤 하위 클래스가 구현하느냐에 따라 달라지는 메소드

***

# Java 기본 구조

## 용어 정리
- 클래스: 실행 시 오브젝트에 관한 소스 코드
- 객체: 클래스의 실체. instance
- 속성: 객체의 데이터 data field, data member, instance varible, data field
- 메서드: 객체의 행위 algorithm, function, procedure
- 생성자: 새로운 객체 초기화
- 패키지: 클래스 또는 하위 패키지를 그룹화

## Source file layout
- 자바 소스 파일의 기본 문법
- pacakage decalaration: 내가 속하는 패키지
- import decalaration: 참조하려는 패키지, 파일, 라이브러리 등
- class decalaration: 해당 클래스 정의

```java
package org.tacademy.basicjava;

import java.util.Vector;

public class HelloJava{
	public statice void main(String[]. args){
		system.out.println("");
		Vector vvv;
	}
}
```

## Software package
- 큰 규모의 소프트웨어 시스템 관리 용이
- 패키지는 클래스위 하부 패키지 포함

##  import 문법
- 어떤 클래스를 사용할건지 컴파일러에게 알려줌
- 모든 클래스 선언 앞 부분에 적음
- import java.util.Vector;

## Java Naming Rule
- Class: UpperCamelCase
    - ex) class Test;
- Method: loewCamelCase 소문자 시작, 기본적으로 동사, set, get, put 등 
    - ex) run();  runFast();
- Variable: lowerCamelCase 소문자시작
    - ex) int abc;

> **Tips:**
> C에서는 언더바 쓸때도 있지만 언더바 쓰는거 아님(자바에서는 상수 정의할 때만 사용) 

- constants: uppercase characters seperated by undersores 
- ex) final static int MAX_POINT = 10; > 상수

## object, variable, type
- objcet : 객체. 존재하는 사물
  - new keyword를 통해 생성됨 ex) new Myobject();
- variable : 객체를 가리키는 이름. 실제 객체를 가리키는것(객체를 갖고 있지 않음)
	* 변수는 객체가 아님
  - int i >> i : 변수, MyObject obj >> obj가 변수
  - variable이 객체를 가리키도록 하기 위해서는 = 사용
  ex) obj = new MyObject();
- Type : class를 나타냄. 홍길동의 객체, 사람이라는 타입
  ex) Person hong = new Person();

> **Tips:**
> 변수를 정의해놓고 객체를 할당하지 않고 
> 그냥 변수를 사용 하는 경우가 많음 >> null pointer exception(변수에 객체를 할당하지않고 사용해서)

## Interface
- 자바에서 중요한 부분임
- 인터페이스는 규약이다. 메소드의 정의만 갖고있는 클래스
- 자바의 인터페이스는 메소드 구현이 없고 형식적인 선언만 되어있다.

### Use of interface
- 어떤 조건이 되면 클래스 외부에 그 내용을 알려주고자할 때 사용
- implements interface : is-a 관계임
- 인터페이스의 객체는 그 클래스의 객체라고 말할 수 있음.
- 어떤 클래스에 특정한 기능을 부여하고 싶을 때 사용
- 클래스의 실제 내용을 보이지않고 프로그래밍 인터페이스만 공개하고자할때 사용
- 몇 개의 인터페이스를 구현한 클래스를 선언함으로써 다중상속의 효과 얻을때

```java
interface Bicycle{
	void changeCadence(int newValue);
	..
}

class MyBicylce implements Bicycle{
// is-a관계
}
```

## Annotation
- 코드의 로직과는 관계가 없음
- 코드 안에서의 메소드, 필드 , 파라메터 등 메타데이터를 기술하기 위해  사용
    - ex) 부모의 메소드 상속받아서 구현할때 @Override라는 어노테이션 사용--> 컴파일러가 해당 슈퍼클래스를 확인해서 메소드가 있는지 확인 작업 등
- runtime: 메모리에 올라간 뒤에서 남겨둠
- Annotaion target : type, method, field 등을 주로 사용 
- 대표적 어노테이션 
   - @Override
   - @Deprecated(더이상 사용하지 않을 기능)
   - @SuppressWarnings
   - @Retention : 어노테이션 정보가 도달하는 곳

## Constructor
- 자바의 컨스트럭터 : 객체를 생성할 때(new 키워드) 호출되는 함수 
- Constructor의 이름은 클래스 이름과 같음
- Constructor가 없으면 자동으로 컴파일러가 default constructor 생성
 - default constructor는 constructor를 기술하지 않아도 new XXX() 해서 객체를 생성할 수 있게 함
 - 다른 constructor가 정의 되면 default constructor 만들지 않음.
 
***

# JAVA 키워드

## Modifier
함수, 클래스, 필드 등의 속성을 나타냄

> **Note**
> 
> - abstract : 정의만 있다. 구현이 없는 method, abstract method를 갖고있는 class
> - final : 필드의 변수에 객체를 할당하면 더이상 변경불가
> - native : 이 메소드의 구현은 자바가 아니라 C로 되어있다
> - private, protected, public : 범위 제한자
> - static : 이 변수는 객체가 사용하는 필드가 아니라 클래스에서 사용하는 필드다. static은 조심해서 사용해야 한다. 버그 발생 위험. 
> - synchronized : 메소드 동시 접근 제어
> - transient : 메모리에서만 값을 유지
> - volatile : 메모리에서 직접 값을 읽고 씀
> - strictfp : 부동소수점 연산은 하드웨어의 기능을 이용하는데, 이 키워드를 쓰면 동일한 부동소수점 연산 가능

## 범위제한자
**private**: 같은 class에서만 사용 가능
<br>
**protected**: 같은 class 또는 상속받은 class에서 사용 가능
<br>
**public**: 모든 class에서 사용 가능
<br>

> 없을 때: packaged로 동일한 package에서만 사용 가능

## Static Keyword
1. 스태틱은 변수와 메소드, 내부 클래스에 쓸 수 있다.
2. static이 붙은 변수와 메소드는 객체에 속하지 않고, 클래스에 속한다.
3. 객체를 생성했을 때만 사용 가능
4. 싱글톤 등 특별한 케이스에 static 붙이는게 낫다.
5. 클래스가 로딩될때 초기화된다.
6. static 변수는 클래스의 모든 객체들 사이에서 공유됨

## Final Keyword
1. final 클래스는 더 이상 상속되지 않는다.
2. final 메소드는 오버라이딩 할 수 없다.
3. final 변수는 한번만 할당 가능, 값이 변경되지 않는 상수

## Single Inheritance
자바는 오직 single inheritance(C++은 다중 상속)

```java
// Subclassing
public class Employee{
    ..
}

public class Manager extends Employee{
    public String department;
}
```

## The super Keyword
1. super는 상위 클래스의 객체를 가리킨다.
2. 하위 클래스에서 상위 클래스의 멤버 변수와 메소드에 접근할 때 사용
3. super(..)와 같은 방식으로 상위 클래스의 생성자에 접근(반드시 sub의 생성자에서 맨 처음 호출해야함)

## The this Keyword
1. this는 클래스의 객체 자신을 가리킨다.
2. 자신의 멤버 변수와 메소드에 접근할 때 사용

```java
// super 클래스
public class BaseClass{
    String message;
    public BaseClass(String message){
        this.message = message;
    }
}
    
    
// sub클래스
public class SubClass extends BaseClass{
    int age;
    public SubClass(String message){
        super(message); 
        //this(message, 0); //아래에 있는 생성자 호출
    }
    
    public SubClass(String message, int age){
        super(message);
        this.age = age;
    }
}
    
```

---------------------------

## Polymorphism
1. 다른 형태를 가질 수 있는 성질
2. 하나의 객체는 오직 하나의 형태만을 가진다.
3. ***레퍼런스*** 변수는 다른 여러 형식의 객체를 가리킬 수 있다.

## Heterogeneous Collections
Homogeneous Collection: 같은 형태의 객체들의 집합

```java
MyDate[] dates = new MyDate[2];
dates = new MyDate(22, 12, 1234);
dates = new MyDate(22, 7, 1234);
```

Heterogeneous Collection: 여러가지 다른 형태의 객체들의 집합
```java
Employee[] staff = new Employee[1234];
staff[0] = new Manager();
staff[1] = new Employee();
staff[2] = new Engineer();
```

## Override Method
- super에 정의되어 있는 method를 sub에서 다시 정의하여 사용하는 것
- method 앞에 @Override annotation을 붙이자
- 리턴 타입은 Override할 method와 일치해야 한다.

## Virtual Method Invocation
Virtual Method
- class 내부에서 함수의 정의만 있고 구현은 없는 함수.
- virtual method가 있는 class는 virtual class가 됨.(반드시 abstract class)

```java
public abstract class BaseClass{    //반드시 abstract class
    public abstract int getAge(); 
}

public class SubClass extends BaseClass{
    @Override
    public int getAge(){..}     //반드시 abstract method 구현
}
```

## Overloading Method 
함수 이름은 같지만 들어가는 파라메터가 다를 때

```java
public void println(int i);
public void println(float f);
public void println();
```

## Overloading Constructors
- 생성자도 오버로딩 가능(단, 오버라이드는 X. 생성자는 상위클래스부터 상속되지 않음)
- 일반적으로 파라메터가 있는 Constructor는 명시적으로 호출하지 않는 이상, 호출되지 않음
- 하지만 Default Constructor만은 명시적으로 호출하지 않아도 호출됨

> static은 클래스가 메모리에 로딩될 때 생성

