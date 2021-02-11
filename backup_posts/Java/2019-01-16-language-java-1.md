---
layout: post
title:  "[Java]Collection"
subtitle:   "[Java]Collection"
description : "Java collections 정리"
keywords : "Java, Collection, 자바컬렉션, 컬렉션, List, ArrayList, Vector, LinkedList, Map, HashMap, HashTable, TreeMap, Properties"
categories: language
tags: java
comments: true
---

> ## Collections  
> Java 컬렉션 정리  

- **Collection**
	- List: 순서 유지 / 중복 저장
		- ArrayList, Vector, LinkedList
	- Set: 순서 유지 X / 중복 저장 X
		- HashSet, TreeSet
- **Map** : 키와 값의 쌍으로 저장 / 키는 중복 X\
	- HashMap, HashTable, TreeMap, Properties(hashtable과 같고, 키 값을 String으로 제한함)

- LIFO, FIFO 컬렉션은 생략
	- Stack
	- Queue

```
- ArrayList, LinkedList
> 빈번한 객체 삭제와 삽입의 경우에는 LinkedList가 좋은 성능.
> 인덱스 검색이나, 맨 마지막에 객체를 추가하는 경우에는 ArrayList가 좋은 성능.
```

```
- Set, HashSet
> HashSet: Hashcode로 검증 후, equals 메소드로 동일한지 판단하여 중복 객체 저장 안함
> Set이나 HashSet은 Iterator를 통해서 반복문(탐색)하거나, 향상된 for문 사용

EX) 1

Set<String> set = HashSet<>();

...add 생략..

Iterater<String> iter = set.iter();
while(iter.hasnest()) {

	String str = iter.next();
}


EX) 2

Set<String> set = HashSet<>();

for(String str : set) {

}
```

```
- HashMap
Map<K, V> map = new HashMap<K, V>();

키 타입은 주로 String을 사용
값 타입은 주로 Wrapper 클래스를 사용함.
(즉, 기본타입 byte, short, int 등을 사용할 수 없음)
```

```
- HashTable, Vectort
> 두가지는 Map과 List를 상속받아 다르지만 큰 공통점이 있다.
> 동기화된(Synchronized) 메소드로 구성되어 있기 때문에 멀티 스레드가 동시에
> 메소드를 실행할 수 없고, 하나의 스레드가 실행을 완료해야 다른 스레드가 실행할 수 있음.
> 즉, 멀티 스레드 환경에서 안전하게 객체를 추가, 삭제 할 수 있다.

예시

[  HashTable ] <- 스레드1 접근 가능.
|K,V .... K,V| <- 스레드2 접근 불가.(스레드 1이 사용 후)
```

```
- TreeSetm TreeMap
> 키는 저장과 동시에 자동으로 오름차순으로 정렬된다.
> 숫자(Integer, Double)은 값으로 기준, String은 유니코드 기준으로 정렬.

p752부터 참고
```

### Synchronized 알아보기
- 둘 이상의 쓰레드가 공동의 자원(파일이나 메모리 블록)을 공유하는 경우, 순서를 잘 맞추어 다른 쓰레드가 자원을 사용하고 있는 동안 한 쓰레드가 절대 자원을 변경할 수 없도록 해야 한다. 한 쓰레드가 파일에서 레코드를 수정하는데, 다른 쓰레드가 동시에 같은 레코드를 수정하면 심각한 문제가 발생할 수 있다. 이런 상황을 처리할 수 있는 한 방법은 관련된 쓰레드에 대한 동기화(synchronization)를 이용하는 것이다.
- 동기화의 목적은 여러 개의 쓰레드가 하나의 자원에 접근하려 할 때 주어진 순간에는 오직 하나의 쓰레드만이 접근 가능하도록 하는 것이다. 동기화를 이용해 쓰레드의 실행을 관리할 수 있는 방법은 두 가지가 있다.
	- 코드를 메소드 수준에서 관리할 수 있다. - 동기화 메소드  
	- 코드를 블록 수준에서 관리할 수 있다. - 동기화 블록  
참고: https://hashcode.co.kr/questions/259/

### 동기화된 컬렉션 자세히 알아보기
- 다시 말하지만, 대부분의 컬렉션 프레임워크는 싱글스레드를 가정하고 설계
- 멀티 스레드가 동시에 컬렉션에 접근시, 의도하지 않게 요소가 변경되는 불안한 부분 발생
- 컬렉션 프레임워크는 비동기화된 메소드를 동기화된 메소드로 래팅하는 메소드를 지원
	- Collections.synchronizedXXX();

```
리턴타입		메소드(매개변수)						설명
List<T>		synchronizedList(List<T> list)		List를 동화기된 List로 리턴
Map<K,V>	synchronizedMap(Map<K,V> map)		Map을 동기화된 Map으로 리턴
Set<T>		synchronizedSet(Set<T> set)	  Set을 동기화된 Set으로 리턴

EX)
List<T> list = Collections.synchronizedList(new ArrayList<>());
```

- 동기화된 컬렉션은 명확하게 느리다는 단점이 있다.
	- 개선을 위해서 = 즉 병렬처리를 지원하기 위해서 지원하는 메소드가 있음
	- 처리하는 요소가 포함된 부분만 잠근하고, 나머지 부분은 다른 스레드가 변경할 수 있도록 하는 것

```
- 병렬 처리를 위한 컬렉션 예시

Map<K,V> map = new ConcurrentHashMap<K,V>();

Queue<E> queue = new ConcurrentLinkedQueue<E>();
```

참고: 책, 이것이 자바다
