+++
title = "c++ 간단 정리"
description = "c++ 예전에 공부했던 내용 정리"
tags = [
    "til",
    "c++"
]
date = "2018-12-31"
menu = "main"
+++

# c++ 예전에 공부했던 내용 정리

## c++의 타입 변환 연산자
- static_cast<>: 가장 기본적인 캐스트 연산 방법
- dynamic_cast<>: 객체지향 언어의 다형성을 이용하여 모호한 타입 캐스팅 오류를 막아줌
- const_cast<>: 자료형이 갖고 있는 상수속성 제거
- reinterpret_cast<>: 어떠한 포인터 타입끼리도 변환할 수 있게 도움

## 자료형 크기
```c++
class Temp {
    int no;
    bool is_no;
}

sizeof(Temp) // 8
```

Temp 클래스는 int 4바이트와 bool 1바이트만 갖고 있어서 5바이트 사이즈라고 착각할 수 있다. 이는 CPU의 데이터 버스와 관련이 있는데, 데이터 버스는 한 번에 4바이트씩 데이터를 담아 이동한다. sizeof는 4바이트 단위로 몇 번 움직이느냐에 따라 결과가 달라진다. Temp 클래스는 5바이트이기 때문에 데이터버스가 2번 이동하여 8바이트이다.

## 문자열 길이
한글 문자열은 1바이트 아스키 문자로 표현 못한다. 2바이트에 한글자를 담기 때문에 문자열 길이가 영문에 비해 두 배다.


## 포인터
- 변수가 실제 값을 저장하는 용도로 사용된다면, 포인터는 변수의 주소를 가리킨다.

```c++
int a = 10;
int *pA = &a; 
// 10이란 정수가 담겨있는 위치를 나타낸 것이 포인터
```

* 아스테리스크가 붙은 변수는 실제 값이 아니라 주소를 저장한다는 뜻

```c++
double b = 10.3;
double *pB = &b;

sizeof(b) // size: 8
sizeof(pB) // size: 4
```

- 포인터는 실제 값이 아니라 주소를 가리키는 화살표만 저장하는데, 이 화살표의 크기가 4바이트이다.
이 사이즈가 중요한데, **함수 인자 넘길때 4바이트만 넘겨도 된다는 뜻**이다. 즉, 성능 올라감

- 함수는 인자로 넘어오는 값을 복사하여 사용하는데, 인자의 사이즈가 크다면 복사하는데 많은 시간이 소요된다. 해당 함수를 자주 호출할 수록 프로그램 성능은 떨어질 것이다. **인자의 크기가 클수록 포인터를 이용하여 4바이트 주소만 넘기는 것이 좋다**

```c++
#include<iostream>
#include<string>
#include<vector>

using namespace std;

int PointerFunc(vector<string> *info) {
    if (info->empty()) 
    	return 0;
    else 
    	return sizeof(info);
}

int main() {
    vector<string> message;
    message.push_back("abcd");
    message.push_back("bcd");
    message.push_back("cd");
    message.push_back("d");
    
    cout << PointerFunc(&message) << endl;  // 4
    cout << sizeof(message) << endl;	    // 16

    return 0;
}
```

## Call By Value, Call By Reference

### Call By Value
- 인자 값을 직접 복사해서 사용하는 것

### Call By Reference
- 인자의 주소를 이용
- 함수 인자로 자료형의 주소를 가리키는 포인터를 받는다 -> 포인터가 가리키는 곳. 즉, 주소의 값을 직접 바꾸는 것

### Call By Address
- 주소를 명시적으로 전달받아 4바이트가 할당 됨
- 함수는 인자로 포인터를 받는데, 이 경우 함수를 호출하는 곳에서 주소를 명시적으로 전달해줘야 함

```c++
#include<iostream>

using namespace std;

void CallByAddress(bool *is_on) {
	cout << "Call by Address: " << sizeof(is_on) << endl;
}

void CallByReference(bool &is_on) {
	cout << "Call by Reference: " << sizeof(is_on) << endl;
}

int main() {
    bool it_tmp = true;
    
    Func1(&is_tmp); // call by address:   4
    Func2(is_tmp);  // call by reference: 1
    
    return 0;
}
```

**변수의 주소를 명시적으로 알려주면 포인터의 크기가 4바이트 인자로 넘어가지만, call by reference(넘어오는 값을 포인터로 받으면)는 자료형의 바이트가 사용된다.**

## const
- 값을 바꿀 수 없는 변수
- 포인터의 위치에 따라 의미가 달라짐

```c++
int number1 = 10;
int number2 = 20;

int const *ptr1;
ptr1 = &number1;
ptr1 = &number2; // 가능

int *const ptr2 = &number1;
*ptr2 = number2;
// *ptr2 = &number2;
```

- ptr1: 가리키는 대상을 변경할 수 있지만, 가리키는 대상의 값은 변경할 수 없다.
- ptr2: 가리키는 대상을 변경할 수 없고, 포인터는 변경 가능

