+++
title = "Advanced Java03 - Java Keyword"
description = "온라인 T아카데미 강의 Advanced Java 정리"
tags = [
    "java",
]
date = "2015-03-07"
menu = "main"
+++


#### Modifier
함수, 클래스, 필드 등의 속성을 나타냄

> **Note :**
> 
> - abstract : 정의만 있다. 구현이 없는 method, abstract method를 갖고있는 class
> - final : 필드의 변수에 객체를 할당하면 더이상 변경불가
> - native : 이 메소드의 구현은 자바가 아니라 C로 되어있다
> - private, protected, public : 범위 제한자
> - static : 이 변수는 객체가 사용하는 필드가 아니라 클래스에서 사용하는 필드다.
           : 조심해서 사용해야 한다. 버그 발생 위험. 
> - synchronized : 메소드 동시 접근 제어
> - transient : 메모리에서만 값을 유지
> - volatile : 메모리에서 직접 값을 읽고 씀
> - strictfp : 부동소수점 연산은 하드웨어의 기능을 이용하는데, 이 키워드를 쓰면 동일한 부동소수점 연산 가능
>

#### 범위제한자
- private: 같은 class에서만 사용 가능
- protected: 같은 class 또는 상속받은 class에서 사용 가능
- public: 모든 class에서 사용 가능
- 없을 때: packaged로 동일한 package에서만 사용 가능

#### The static Keyword
1. 스태틱은 변수와 메소드, 내부 클래스에 쓸 수 있다.
2. static이 붙은 변수와 메소드는 객체에 속하지 않고, 클래스에 속한다.
3. 객체를 생성했을 때만 사용 가능
4. 싱글톤 등 특별한 케이스에 static 붙이는게 낫다.
5. 클래스가 로딩될때 초기화된다.
6. static 변수는 클래스의 모든 객체들 사이에서 공유됨

#### The final Keyword
1. final 클래스는 더 이상 상속되지 않는다.
2. final 메소드는 오버라이딩 할 수 없다.
3. final 변수는 한번만 할당 가능, 값이 변경되지 않는 상수

#### Single Inheritance
- 자바는 오직 single inheritance(C++은 다중 상속)

```java
// Subclassing
public class Employee{
    ..
}

public class Manager extends Employee{
    public String department;
}
```

#### The super Keyword
1. super는 상위 클래스의 객체를 가리킨다.
2. 하위 클래스에서 상위 클래스의 멤버 변수와 메소드에 접근할 때 사용
3. super(..)와 같은 방식으로 상위 클래스의 생성자에 접근(반드시 sub의 생성자에서 맨 처음 호출해야함)

#### The this Keyword
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

#### Polymorphism
1. 다른 형태를 가질 수 있는 성질
2. 하나의 객체는 오직 하나의 형태만을 가진다.
3. ***레퍼런스*** 변수는 다른 여러 형식의 객체를 가리킬 수 있다.


#### Heterogeneous Collections

Homogeneous Collection:  같은 형태의 객체들의 집합

```java
MyDate[] dates = new MyDate[2];
dates = new MyDate(22, 12, 1234);
dates = new MyDate(22, 7, 1234);
```

Heterogeneous Collection:  여러가지 다른 형태의 객체들의 집합

```java
Employee[] staff = new Employee[1234];
staff[0] = new Manager();
staff[1] = new Employee();
staff[2] = new Engineer();
```

#### Override Method
- super에 정의되어 있는 method를 sub에서 다시 정의하여 사용하는 것
- method 앞에 @Override annotation을 붙이자
- 리턴 타입은 Override할 method와 일치해야 한다.


#### Virtual Method Invocation
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

#### Overloading Method 

함수 이름은 같지만 들어가는 파라메터가 다를 때

```java
public void println(int i);
public void println(float f);
public void println();
```

#### Overloading Constructors

- 생성자도 오버로딩 가능(단, 오버라이드는 X. 생성자는 상위클래스부터 상속되지 않음)
- 일반적으로 파라메터가 있는 Constructor는 명시적으로 호출하지 않는 이상, 호출되지 않음
- 하지만 Default Constructor만은 명시적으로 호출하지 않아도 호출됨

> static은 클래스가 메모리에 로딩될때 생성

