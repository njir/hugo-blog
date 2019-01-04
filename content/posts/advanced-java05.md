+++
title = "Advanced Java05 - Exceptions"
description = "온라인 T아카데미 강의 Advanced Java 정리"
tags = [
    "java",
]
date = "2015-03-07"
menu = "main"
+++


#### Java의 Error 처리
- 자바는 Error 또는 Exception 상황에 Throwable이라는 객체를 생성해서 던짐
- Throwable : Exception, Error로 구성
- Exception은 개발자가 처리할 수 있는, recoverable한 에러
- Runtime Exception, IOException, Null Point 등 다양한 Exception이 있음
- Error : 코드상에서 처리할 방법이 없는 Error
- Throwable은 Error와 RuntimeException을 제외하고 명시적으로 throws하거나 try catch 해줘야함


#### The try and catch Statements

```java
try{
	//Exception이 발생
}
catch(MyExceptionType myExcept){
	// MyExceptionType의 exception이 발생하면 실행
	// 위쪽에 있는 익셉션이 더 작은 sub exception 이어야함
}	
catch(Exception otherExpect){
	//상위 클래스로서 일반적인 Exception의 경우 처리되는 부분
}finally{
	//정리할 리소스들 처리 등
	//try, catch 어디를 가든 finally 코드블럭 처리해줌
}
```

#### Call Stack Mechanism
- 예외가 발생하고 처리하는 블록이 없을 경우 해당 코드를 호출한 메소드로 거슬러 올라간다.
- main()까지 올라가서도 없으면 프로그램 종료

#### Runtime Exception
- ArithmeticException : 0으로 나누는 경우 등
- NullPointerException : 객체를 할당하지 않고 그 변수에 접근
- ArrayIndexOutOfBoundsExceptio : 배열 잘못 접근
- NegativeArraySizeException
- SecurityException : Applet에서 많이 발생
> **Tips:**  
Error, RuntimeException은 명시적으로 try~catch 처리하지 않는다.

#### Method Overriding Exception
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