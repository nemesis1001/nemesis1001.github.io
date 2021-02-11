---
layout: post
title:  "[Scala]헬로, 스칼라"
subtitle:   "[Scala]헬로, 스칼라"
description : "Scala "
keywords : "Scala, oop문법, oop, 스칼라, 함수형프로그래밍"
categories: language
tags: scala
comments: true
---

> ### Hello, Scala  
> 스칼라 프로그래밍 시작하기  
> 변수와 Type예약어 알아보기  

#### Hello Scala

```
println("헬로, 스칼라")

헬로, 스칼라
```

- 스칼라는 new를 통해 인스턴스를 생성하지 않고, 하나의 인스턴스만 생성 가능한 싱글턴 객체 이다.
- static 예약어가 필요하지 않으며, object 예약어가 싱글턴 객체로 만든다.
	- 즉, static 기능이 필요하다면 **object 안에 구현하고 가져다가 사용** 하면 된다.

<br>

#### 변수다루기

```
var a = "변수입니다."
val b = "final 변수입니다."
a = "a 변수를 바꿔봅니다"
b = "b 변수를 바꿔봅니다"

a: String = 변수입니다.
b: String = final 변수입니다.
a: String = a 변수를 바꿔봅니다
<console>:25: error: reassignment to val
       b = "b 변수를 바꿔봅니다"
         ^
```  

- var과 val의 차이점은 not final, final이다
- 자동적으로 String 자료형을 판단하고, val로 동시성 문제를 해결한다.  

<br>

```
(1) 초기화 안할 시 컴파일 에러
var newA

<console>:3: error: '=' expected but ';' found.
print("")
^


(2) 초기화 필요
var newA = 10

newA: Int = 10


(3) 타입을 지정해주는 것을 권장
var newB: Int = 10

newB: Int = 10
```
- (1)처럼 초기화하지 않으면 컴파일 에러발생 -> 스칼라는 확실한 가이드라인 필요
- (2)타입을 추론함 Int형, 초기 값이 없는 경우 **None** 으로 초기화가 권장된다
- (3)변수명 뒤 (:)을 사용해, 사용자가 타입을 정하는 것을 권장함

<br>

#### 기본 자료형과 참조 자료형

<img src="https://camo.githubusercontent.com/911b0fb8a291ad5b03965ac92a1073fe89e0aa60/68747470733a2f2f7777772e7363616c612d6578657263697365732e6f72672f6173736574732f7363616c615f7475746f7269616c2f7363616c615f747970655f6869657261726368792e706e67">

- Unit 타입의 경우에는 void와 같다. 특정한 값을 나타내지 않고 비어있다는 것
- AnyRef는 **사용자가만든 객체** 와 **AnyVal이 아닌 내장 객체** 로 나뉜다  
- AnyRef는 Java의 Object라고 생각하자.

<br>

#### Type 예약어 사용

```
type Name = String
type Person = (String, Int)
type FType = String => Int

defined type alias Name
defined type alias Person
defined type alias FType


val name:Name = "LSH" //변수 name에 Name 자료형
val person:Person = ("LSH", 20) //변수 name에 Person(튜플)
val f:FType = text => text.toInt //text라는 매개변수를 받는 함수

name: Name = LSH
person: Person = (LSH,20)
f: FType = <function1>
```

- Name은 String 자료형을 받음, Person은 (String, Int)쌍인 튜플
- FType은 String을 받아서 Int를 반환함
