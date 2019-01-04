+++
title = "Advanced Java06, 07 - RTTI, Java annotation 및 Generic"
description = "온라인 T아카데미 강의 Advanced Java 정리"
tags = [
    "java",
]
date = "2015-03-10"
menu = "main"
+++


#### RTTI(Run-Time Type Information)



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

#### RTTI의 필요성
구조화된 프로그래밍을 위해서

- annotation: Java class파일에 meta 데이터를 기술하는 방식
- annotation이 어디까지 정보를 가져갈꺼냐 -> @Retention

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

#### Reflect

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
 - 이미 코딩된 Class의 경우 getClassLoader()로 얻어올 수 있음
- Proxy Class
 - 인터페이스를 implements한 class나 객체를 동적으로 생성해 주기 위한 class
 - reflect를 이용할 때 reflect로 생성한 object에 등록할 interface를 implements한 객체를 동적으로 생성
- Invocation Handler
 - 내부에 invoke 함수를 통해서 method의 이름을 얻어와서 해당 method에 대한 처리 결과를 return

#### Java Generic

- 클래스에 사용할 타입을 사용자가 지정할 수 있도록 임의의 타입을 지정하는 것
- 다양한 타입을 사용하기 위해서 + **컴파일 타임의 type safety를 보장하기 위해(특정한 타입으로 확정되도록)**
 - ArrayList에 들어가는 타입을 마음대로 바꿀 수 있다.
 - ArrayList<String> list : **오직 String만 다루는 ArrayList**
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
