---
layout: post
title:  "[Scala]스칼라 알아보기"
subtitle:   "[Scala]스칼라 알아보기"
description : "Scala 알아보기"
keywords : "Scala, oop문법, oop, 스칼라, 함수형프로그래밍"
categories: language
tags: scala
comments: true
---

> ### Scala 알아보기  
> 스칼라 언어와 자바언어 차이를 알아보기  
> 제플린 설치하기  


#### code로 java vs scala 차이 알아보기  

```
//Java code
List<Integer> otherList = new ArrayList<Integer>();
int number = 0;

for(int i = 0; i< list.size(); i++){
	number = list.get(i);
	if(number >= 5){
		otehrList.add(number);
	}
}

//Scala code
val otherList = list.filter( i => i>=5)
```

#### Scala의 장점을 알아보자
- 풍부한 표현식과 연산자  

```
animal.eat(fruit) //java  
animal eat fruit //scala
```  

- 동시성에 강한 언어  
	- 스칼라는 Immutable 객체를 기본으로 한다. 값은 참조일뿐 값을 변경할 수 없다.
	- Immutable은 쓰레드 세이프하며, 객체 상태 변경이 없어서 간단하게 동작한다.

- 객체지향 + 함수형언어
	- 스칼라는 함수의 매개변수에 함수 객체를 넣을 수 있고, 변수에 함수 할당이 가능
	- 만약 이러한 기능이 제공이 안되면 자료형을 반환하는 함수를 다시 설계해야함  

```
Member.members.filter(_userId === "admin").delete //scala  
만약 자바 였다면, for문 사용과 if조건문이 필요했을 것.  
```  

- 맥락을 읽는 언어, implicit

- 자바와의 연계성이 뛰어남
	- 자바 라이브러리를 전부 사용이 가능함. JVM을 사용하기 때문

<br>

> 앞으로 코드 실습은 zeppelin을 사용합니다  
> 제플린 설치하기 [바로가기](https://twowinsh87.github.io/data/2018/07/31/data-fcdes-zeppelin-setup-1/)
