---
layout: post
title:  "[Spark_3]Spark의 Transformation vs Actions, 영속화"
subtitle:   "[Spark_3]Spark의 Transformation vs Actions, 영속화s"
categories: data
tags: fcdes
comments: true
---


## [Spark_3]Spark의 Transformation vs Actions, 영속화

> Transformation vs Actions 그리고 영속화에 대해서 알아보자

### RDD의 연산
- Transformation
	- 새로운 RDD를 만들어 내는 연산으로 Action을 하기 전까지 transformation은 일어나지 않는다.
	- transformation의 return타입은 다른 RDD타입으로 바뀜(메타 데이터만 가지고 있는), 한번 만든 RDD는 imutable

- Action
	- Transformation을 실행시킨다. action의 return타입은 RDD에서 다른 타입으로 나온다.

### Transformation, Action의 공식문서
- **[공식문서](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)**


### RDD의 Transformation(일부)

| 함수 이름  | 용도  | 예 |
|:------------- |:---------------:| :-------------:|
| map()       | RDD의 각 요소에 함수를 적용하고 결과 RDD를 되돌려 준다 |         rdd.map(x=>x+1)  |
| flatMap()      | RDD의 각 요소에 함수를 적용하고 반환된 반복자의 내용들로 이루어진 RDD를 되돌려 준다. 종종 단어 분해를 위해 쓰인다        |           rdd.flatMap(x=>x.to(3)) |
| filter()  | 함수의 조건을 통과한 값으로 이루어진 RDD를 되돌려 줌	        |            rdd.filter(x=> x!=1)  |
|distinct() | 중요함. 중복을 제거.log에서 중복제거 할 때, 네트웍 문제로 중복된 것이 올라오거나, 사용자 수 카운트 할 때, id.distint 이런식으로사용.하지만 비싼 연산 |rdd.distinct()|
|sample(withReplacement, fraction, [seed])| 복원 추출(withReplacement=true)이나 비복원 추출로 RDD에서 표본을 뽑아낸다. fraction은 전체 데이터의 비율값(0.5 = 50%) 추출. 즉 일부만 뽑는 것, parameter가 3개. fraction이 중요한데 0.1은 10% 이런식 |rdd.sample(false, 0.5)  |
|union() |두 RDD에 있는 데이터들을 합한 RDD를 생성 |rdd1 union rdd2 |
|intersection() |양 RDD에서 교집합에 해당하는 값만 추출 |rdd1 intersection rdd2 |
|subtract()  |한쪽 RDD가 가진 데이터를 다른쪽에서 삭제, (예, 교육용 데이터삭제) |rdd1 subtract rdd2  |
|cartesian()  |두 RDD의 카테시안곱 |rdd1 cartesian rdd2  |
|repartition()  |파티션을 다시 나누는 것. 해야하는 경우는 Mysql에 넣어야하는데 partition이 너무 잘게 나눠져 있거나 혹은 파티션이 1000개로 나눠져 있다면 save시에 1000개로 저장됨. 그래서 추후 관리하는데 문제가 생길 수 있다고 생각하면, 재조정을 통해 줄이거나 하기 위해 사용한다. | |
|groupByKey |group하면 밸류가 list형태로 나옴(K, Iterable<V>) 만약 레코드가 1억개 -> groupByKey를 해서 키로 모으고, 밸류로 세세하게 나눈 것이 메모리를 넘치는 경우가 생길 수 있음. | |
|mapParitions  |rdd 에서 map 함수를 이용해서 mysql에 데이터를 넣고 싶다. 할 때 사용  | |

<br>
---


### RDD의 Action(일부)

| 함수 이름  | 용도  | 예 |
|:------------- |:---------------:| :-------------:|
|collection()  |RDD의 모든 데이터 요소 리턴 Array로 모으는 것. 메모리를 넘지않을 작은 데이터 일 때 사용  | |
|count()  |RDD의 요소 개수 리턴  | |
|countByValue()  |RDD에 있는 각 값의 개수 리턴 ReduceByKey와 비슷  | |
|take(num)  |RDD의 값들 중 num개 리턴  | |
|top(num)  |RDD의 값들 중 상위 num개 리턴 | |
|takeOrdered(num)(ordering)  |제공된 ordering 기준으로 num개 값 리턴 | |
|takeSample(withReplacement, num, [seed])  |무작위 값들 리턴 |rdd,takeSample(false, 1) |
|reduce(func)  |RDD의 값들을 병렬로 병합 연산한다 |rdd.reduce((x,y)=>x+y) |
|flod(zero)(func)  |reduce()와 동일하지만 제로밸류를 넣어 준다  |rdd.fold(0)((x,y)=>x+y)  |
|aggregate(zeroValue)(seqOp, comOp)  |reduce()와 유사하나 다른 타입을 리턴한다.  | rdd.aggregate((0,0))  ((x,y)=> (x._1+y, x._2),  (x,y) => (x._1+y._1, x._2+y._2))  |
|foreach(func)  |RDD의 각 값에 func을 적용한다.  RDD에 바로 사용하면 사용되는 것 같지 않다. 어딘가 워커안에서 프린트되고 있는 것.  val d = sc.makeRDD(List(1,2,3,3,3,4,4,5))  d.take(3).foreach(println)  // action에 해당하는 take 명령 등으로 rdd가 아닌 다른 타입으로 리턴 후 foreach를 한다.  //rdd는 action 후에 rdd 타입이 아니다.  즉, d.foreach(println) <-이런 실수를 발생하지 않도록하자 | |
|saveAsTextFile  |text파일로 저장 | |

<br>

### 추가로 알아보기[method 비교, fold() vs reduce()]
- fold와 reduce는 결과 값의 타입이 RDD 내의 연산하는 데이터 요소들의 타입과 동일해야함
- 합계를 구할 때는 원칙을 지키기가 쉽지만, 다른 타입을 결과로 쓰고 싶을 때도 있음.
- 예를들어, 평균을 계산하고자 하면 합계와 각 요소의 개수를 쌍으로 관리해야 함.

```
scala> val input = sc.makeRDD(List(1,2,3,4,5,6,7,8),3) // values depending on the number of partitions. default is your number of core

scala> input.reduce((x,y)=>x+y)
res61: Int = 36

scala> input.fold(0)((x,y)=> x+y)
res62: Int = 36

scala> input.fold(1)((x,y)=> x+y)
res63: Int = 40
```

**[fold() vs reduce() 참고하기](http://wpcertification.blogspot.com/2016/02/difference-between-reduce-and-fold.html)**  

### 영속화, 캐싱
- RDD는 lazy execution 방식이지만, 때때로는 동일한 RDD를 아래처러 여러번 호출해야 할 때가 있다.  

```
scala> val input = sc.makeRDD(List(1,2,3))

scala> val result = input.map(x => x*x)

scala> print(result.count())

scala> print(result.collect().mkString("/“))
```  

- 즉 위와 같을 때, RDD에서 호출하는 액션들에 대해 모든 의존성을 재연산하게 됨.  예제 처럼 개수를 세고 동일한 RDD에 결과를 쓰는 행위.  
- 그런데, RDD의 캐싱을 요청하면 RDD들은 캐싱시점까지 RDD를 계산한 노드들은 파티션을 저장함.  여러 옵션이 있지만(공식문서 참고), 아래와 같이 가볍게 사용하자.

### 캐싱실습
```
scala> val cachinput = sc.makeRDD(List(2,4,6))

scala> val cachresult = cachinput.map(x => x*x)

//persist옵션이 여러개 있지만, 메모리에만 쓰는 memory.only가 default 일 것.
//직접 메모리까지 올리는 작업을 함. 데이터가 많아지면 조금 오래 걸릴 수 있음.
scala> cachresult.persist()

// boolean값 차이는 거의 없다(?), 직접 캐시에서 데이터를 삭제하는 명령어 unpersist().
scala> cachresult.unpersist(true or false)
```  

### 캐싱정리
- 만약 메모리에 많은 데이터를 올리려고 하면 Spark는 LRU 캐시 정책을 따라서 오래된 파티션들을 자동으로 버림.
- 옵션값에 따라서 디스크에 쓰는 경우도 있다.(공식문서나 learning spark p56참고)
- 직접 캐시에서 데이터를 삭제할 수 있도로 RDD는 unpersist()를 사용할 수 있다.
