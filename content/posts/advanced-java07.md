+++
title = "Advanced Java 08 - Java API를 활용한 객체 활용 및 데이터 처리 기법"
description = "온라인 T아카데미 강의 Advanced Java 정리"
tags = [
    "java",
]
date = "2015-03-12"
menu = "main"
+++

#### Java Array
- 동일한 Type의 데이터를 여러 개 가질 때 사용하는 자료구조
- String [] stringArray; 변수
- Array의 객체 생성은 new String[size] 생성
- Array가 생성되었다고 Array에 들어가는 객체가 생성된 것은 아님(초기 null값)

#### Java Array 연산(java.util.Arrays)
- Array로부터 List 생성
 - asList(T... array)
- Array에서 검색, 정렬
 - binarySearch, sort
- Array 복사
 - copyOf, copyOfRange
- Array의 값 비교
 - deepEquals, equals
- Array에 값 채우기
 - fill
 
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


 
 
#### Java Collection Framework
- 자료의 집합 형태에 대한 표준적인 Framework을 정의한 인터페이스/유틸리티의 목록(데이터들의 묶음)
 - Collection : 데이터들을 하나씩 모은 것
 - map : key, value 형식으로 key를 갖고 처리할 수 있는 데이터들의 집합
 

#### Data Structure/Algorithm
- 스택
- 큐
- 링크드 리스트
- 해쉬 : key값을 이용해 해쉬테이블로 다이렉트 매핑 가능, 성능 때문에 사용

#### Java Collection Framework 구조
- Collection Interface
- Map Interface
- Iterator
 - 콜렉션의 데이터들을 하나씩 반복적으로 참조할 때 
 
#### java.util.Colletion
- 콜렉션 내에 adding, clearing, comparing, retaining와 같은 동작을 포함
- 메소드 종류
 - add, addAll : 객체 더하기
 - contains, containsAll, isEmpty, size : 내용 조사
 - clear, remove, removeAll : 객체 제거
 - toArry : 배열로 전환
 - iterator : 반복자로 전환
- Collection의 연산(java.util.Colletions)
 - addAll(Collection<? super T> c, T... a)
 - binarySearch, indexOfSubList, lastIndexOfSubList, sort, copy, nCopies
 - max, min, frequency, reverse, rotate, shuffle, singleton, replaceAll, swap
 - enumeration, list(Enumeration<T> enumeration)
 

