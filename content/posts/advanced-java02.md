+++
title = "Advanced Java02 - 오브젝트, 예외처리, 제너릭"
description = "온라인 T아카데미 강의 'Advanced Java 정리' 오브젝트, 예외처리, 제너릭에 대한 내용"
tags = [
    "java",
]
date = "2015-03-07"
menu = "main"
+++

# Java Object

## Interface
- class 정의시 "어떤 정보가 어떻게 되면 외부에 알려준다."와 같은 경우에 사용
- 예를 들어 함수 정의시 변수가 10의 배수가 되면 외부에 알려주고 싶을 때 사용

```java
public interface CounterListener {
	public voic onNotifyCount(int count);
}

public class Counter {
	private int mCount=0;
	CounterListener mListener;
	public void setCounterListener(CounterListener listener) {
		mLisener = listener;
	}
	
    public void inc(){
	    mCount++;
	    if(mCount % 10 ==0) {
		    //notify out side
		    if(mListener != null) {
				mListener.onNotifyCount(mCount);
			}
		}
	}
}
```

- 공통적으로 사용되는 속성을 정의하고자 할 때
- 객체간 값을 비교하는 속성을 정의하고자 하는 경우 해당 속성을 interface로 정의하고 class에서 이 interface를 implements하게 한 다음, 해당 class의 객체를 interface type으로 변환하여 사용

## inner 클래스
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

## Anonymous 클래스
- 이름 없이 class를 정의하여 객체를 생성한 class

## The instanceof Operator
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

## Cating Object
- 객체를 캐스팅함으로써 그 객체의 모든 멤버에 접근할 수 있다.
- (class의 type) 객체

## Object Class
- Object 클래스는 모든 클래스의 최상위 클래스
- 즉, 자바에서의 모든 클래스의 super class

## The '==' Operator Compared With the equals Method
- '==' 연산자는 두 개의 레퍼런스가 같은지를 판단한다.(그 객체가 같은지) 같은 객체인지를 판단
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

## The toString Method
- 자바의 모든 객체는 string 연산 가능
- 객체를 String으로 바꿀 때 사용

## Wrapper Classes
- Primitive 타입(int, char, byte...)을 오브젝트처럼 다룰 수 있다.
- 자바는 Primitive 타입에 해당하는 각 오브젝트 클래스를 정의하고 있다.

Primitive Type     | Wrapper Class <br>
boolean 		   | Boolean <br>
byte    		   | Byte <br>
char     | Characater <br>
... | ...

## Object Runtime 구조(자바 VM)
- 자바는 스택이 있고 스택 안에 Local Variable Array가 있다. (```int i, j=30 ...```) 변수들이 있는 공간
- HEAP : new로 생성할 때, new로 만든 객체가 들어감
- CLASS AREA : 내가 만든 class , 코드가 메모리에 올라가는 곳
- 바이트 코드 : stack 기반의 명령어 + 객체지향 명령어로 구성

***

# Exceptions

## Java의 Error 처리
- 자바는 Error 또는 Exception 상황에 Throwable이라는 객체를 생성해서 던짐
- Throwable : Exception, Error로 구성
- Exception은 개발자가 처리할 수 있는, recoverable한 에러
- Runtime Exception, IOException, Null Point 등 다양한 Exception이 있음
- Error : 코드상에서 처리할 방법이 없는 Error
- Throwable은 Error와 RuntimeException을 제외하고 명시적으로 throws하거나 try catch 해줘야함


## The try and catch Statements

```java
try {
	//Exception이 발생
}
catch(MyExceptionType myExcept) {
	// MyExceptionType의 exception이 발생하면 실행
	// 위쪽에 있는 익셉션이 더 작은 sub exception 이어야함
}	
catch(Exception otherExpect) {
	//상위 클래스로서 일반적인 Exception의 경우 처리되는 부분
} finally {
	//정리할 리소스들 처리 등
	//try, catch 어디를 가든 finally 코드블럭 처리해줌
}
```

## Call Stack Mechanism
- 예외가 발생하고 처리하는 블록이 없을 경우 해당 코드를 호출한 메소드로 거슬러 올라간다.
- main()까지 올라가서도 없으면 프로그램 종료

## Runtime Exception
- ArithmeticException: 0으로 나누는 경우 등
- NullPointerException: 객체를 할당하지 않고 그 변수에 접근
- ArrayIndexOutOfBoundsExceptio: 배열 잘못 접근
- NegativeArraySizeException
- SecurityException: Applet에서 많이 발생

> **Tips:**  
Error, RuntimeException은 명시적으로 try~catch 처리하지 않는다.

## Method Overriding Exception
- 오버라이딩한 메소드에서는 상위 메소드에서 던져진 예외나 또는 그 예외의 하위 클래스 예외만을 던질 수 있다.

```java
public class MyException extends IOException{
	public MyException(){
		super();
	}
	public MYException(..){..}
	public MYException(..){..}
	public MYException(..){..}
	..
}
```

```java
public class MyCalClass{
	public int add(int a, int b)) throws MyException{
	if(a<0 || b <0){
		throw new MyException("negative value a or b");
		}
		return (a+b);
	}
	public void onCreate(){
		try {
			int v = add(10,-5);
		} catch(MyException e){
			e.printStackTrace();
		}
	}
}
```

> **Tips:**  
자바에서는 에러처리를 할 때, 코드상에서 발생하는 에러 같은 경우 리턴값으로 처리하지 말 것

***

# RTTI

## RTTI(Run-Time Type Information)
- 일반적으로 자바는 동적 resolving이 가능하다.
- 자바 프로그램의 실행 중에 타입 정보를 알아낼 수 있으며 사용 가능
- 라이브러리를 만들 때 RTTI를 사용하는 경우가 종종 있음
- C에서는 .o 파일들을 서로 linking해 실행파일 생성 : Linking
 - 이 실행파일을 RAM에 올릴 때 주소를 재배치하는 과정 : relocation
 - (C에서는 컴파일 때 미리 다 해놓음)
- 자바에서는 
 - b.java -> b.class로, a.java -> a.class로 컴파일
 - jar 파일 생성 -> jar파일이 VM 상에서 a.class가 실행됨(메모리 로딩)
 - a.class가 실행되고 b의 class를 참조하는 시점에 b.class를 가져와서 메모리에 로딩(사용하려는 그 시점에 relocation, 이거때문에 자바가 느림) 
  - 즉, 자바는 실행 중에 필요한게 있으면 그 때 메모리에 로딩하고, 관련 주소를 재배치하고 호출

## RTTI의 필요성
구조화된 프로그래밍을 위해서
- annotation : Java class파일에 meta 데이터를 기술하는 방식
- annotation이 어디까지 정보를 가져갈꺼냐 -> ```@Retention```

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetetionPolicy.RUNTIMe)
public @interface MyAnnotation{
	int num();
    String name() default "MyName";
    String id();
}

@MyAnnotation(num=1, name="MainActivity", id="1")
public class MainActivity extends Activity{
	@Override
    @MyAnnotation(num=1, name="onCreate", id="2")
    public void onCreate(Bundle savedInstanceState){..}
}
```

## Reflect
- java reflect : 이름으로 동적으로 java class를 다루는 방법
 -  자바에서는 자바 클래스의 이름을 갖고 메모리에 로딩
 - class의 method나 field를 이름과 오버로딩한 함수들의 파라메터(signature)를 이용하여 함수 호출 또는 값 설정 

```java
//Class 획득
Class clazz = Class.forName("org.tacademy.basic.MyClass");
Class clazz = MyClass.class;

//객체의 생성
Object obj = clazz.newInstance();
Constructor c = clazz.getConstructor(String.class, Integer.class);
Object obj = c.newInstance("test", 1);

//Method 획득
Method method = clazz.getMethod("myMethod".String.class, Integer.class);
Method method = clazz.getDeclaredMethod("myPrivateMethod".String.class, Integer.class);

//Method 호출
method.invoke(myClassObject, "param1", 10); //instance method
method.invoke(null, "parapm1", 10); //static method

//Field의 획득
Field field = clazz.getField("myField");
```

- ClassLoader
 - Class를 메모리에 로딩하기 위해 사용하는 class
 - 이미 코딩된 Class의 경우 ```getClassLoader()```로 얻어올 수 있음
- Proxy Class
 - 인터페이스를 implements한 class나 객체를 동적으로 생성해 주기 위한 class
 - reflect를 이용할 때 reflect로 생성한 object에 등록할 interface를 implements한 객체를 동적으로 생성
- Invocation Handler
 - 내부에 invoke 함수를 통해서 method의 이름을 얻어와서 해당 method에 대한 처리 결과를 return

***

# Java Generic
- 클래스에 사용할 타입을 사용자가 지정할 수 있도록 임의의 타입을 지정하는 것
- 다양한 타입을 사용하기 위해서 + **컴파일 타임의 type safety를 보장하기 위해(특정한 타입으로 확정되도록)**
 - ArrayList에 들어가는 타입을 마음대로 바꿀 수 있다.
 - ```ArrayList<String> list``` : **오직 String만 다루는 ArrayList**
- Collection 등에 많이 사용

```java
//정의
public class MyClass<E,T>{
	E data;
    T operator;
    public void setData(E data){
    	this.data = data;
    }
    public void setOperator(T operator){
    	this.operator = operator;
    }
    public E getData(){
    	return data;
    }
    public T getOperator(){
    	return operator;
    }
}
// 사용
MyClass<Integer, String> mData = new MyClass<Interger, String>();
mData.setData((Integer)100);
mData.setOperator("add");
```

- Java Generic Type의 제한

```java
// 얘를 상속받은 애들만 사용 가능
public class MyClass<T extends String>{..} 
```

- Java Generic Method

```java
public<T> void putData(T data){..}
public static<T> void printObject(T data){..}
```

- Java Generic Type을 확정할 수 없는 경우

```java
public printLog(MyClass<?> p){..}
```

***

# 객체 활용 및 데이터 처리 기법

## Java Array
- 동일한 Type의 데이터를 여러 개 가질 때 사용하는 자료구조
- ```String [] stringArray;```
- Array의 객체 생성은 new String[size] 생성
- Array가 생성되었다고 Array에 들어가는 객체가 생성된 것은 아님(초기 null값)

## Java Array 연산(java.util.Arrays)
- ```asList(T... array)```: Array로부터 List 생성
- ```binarySearch, sort```: Array에서 검색, 정렬
- ```copyOf, copyOfRange```: Array 복사
- ```deepEquals, equals``` : Array의 값 비교
- ```fill```: Array에 값 채우기
 
```java
public class MyData{
	String name;
    int age;
    public MyData(String name, int age){
    	this.name = name;
    	this.age = age;
    }
}

public class ArrayTest{
	String[] dataArray;
	String[] dataArray2 = {"a", "b", "c"};
    String[] dataArray3;
    
    public void makeArray(){
    	int size = 3;
        dataArray = new String[size];
        dataArray[0] = "a";
        dataArray[1] = "b";
        dataArray[2] = "c";
        int length = dataArray.length;
        
        dataArray3 = new String[]{"a", "b", "c"};
        List<String> list = Array.asList(dataArray);
        setString("a", "b", "c");
        Array.sort(dataArray, new Comparator<String>(){
        	@Override
            public int compare(String str1, String str2){
            	return str1.compareTo(str2);
            }
        });
    }
    public void setString(String... strings){	//파라메터로 동일한 type의 array가 가변적으로 들어올때
    	int length = strings.length;
        for (int i=0; i<length; i++){
        	System.out.println("i : " + i + ", value : " + strings[i]);
        }
    
    }
    
    MyData[] myDataArray = {new MyData("a", 1), new MyData("b", 2), new MyData("c", 3)};
}
```

## Java Collection Framework
- 자료의 집합 형태에 대한 표준적인 Framework을 정의한 인터페이스/유틸리티의 목록(데이터들의 묶음)
 - Collection : 데이터들을 하나씩 모은 것
 - map : key, value 형식으로 key를 갖고 처리할 수 있는 데이터들의 집합

## Data Structure/Algorithm
- 스택
- 큐
- 링크드 리스트
- 해쉬 : key값을 이용해 해쉬테이블로 다이렉트 매핑 가능, 성능 때문에 사용

## Java Collection Framework 구조
- Collection Interface
- Map Interface
- Iterator
 - 콜렉션의 데이터들을 하나씩 반복적으로 참조할 때 
 
## java.util.Colletion
- 콜렉션 내에 adding, clearing, comparing, retaining와 같은 동작을 포함
- 메소드 종류
 - ```add, addAll``` : 객체 더하기
 - ```contains, containsAll, isEmpty, size``` : 내용 조사
 - ```clear, remove, removeAll``` : 객체 제거
 - ```toArray``` : 배열로 전환
 - ```iterator``` : 반복자로 전환
- Collection의 연산(java.util.Colletions)
 - ```addAll(Collection<? super T> c, T... a)```
 - ```binarySearch, indexOfSubList, lastIndexOfSubList, sort, copy, nCopies```
 - ```max, min, frequency, reverse, rotate, shuffle, singleton, replaceAll, swap```
 - ```enumeration, list(Enumeration<T> enumeration)```

