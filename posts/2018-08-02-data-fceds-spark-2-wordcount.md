---
layout: post
title:  "[Spark_2]zeppelin에서 wordcount 예제"
subtitle:   "[Spark_2]zeppelin에서 wordcount 예제"
categories: data
tags: fcdes
comments: true
---

## [Spark_2]zeppelin에서 wordcount 예제

> zeppelin에서 wordcount를 해보자

1. wordcount 예제

 **버전 정보**  
 Java version 1.8  
 //Hadoop version: 3.0  
 Spark version: 2.3  
 Scala version: 2.11.12  
 //Python version: 3.6.4
 maven 3.3.9  
 zeppelin v0.8.0-rc5  

```
val text = sc.textFile("../README.md") // Create RDD, 원하는 파일 경로를 잘 적으세요.
text.take(10).foreach(println)

val split_map = text.map(s => s.split(" ")) //map사용 띄어쓰기로 잘라봅니다.
split_map.take(10)

val split_flatMap = text.flatMap(s => s.split(" ")) //map과 flatMap으로 잘랐을 때 차이는?
split_flatMap.take(10)

//위에서 map flatMap 가장 큰 차이는
//map return type: Array[Array[String]] = Array(Array(#, Apache, Zeppelin), Array(""), Array(**Documentation:** 등등
//flatMap return type: Array[String] = Array(#, Apache, Zeppelin)
//아무래도 word 카운트에서는 flatMap으로 하나씩 펼치듯이 해주는 것이 낫네요

//의미없는 w => w,1로 키/밸류를 만듭니다(추후 밸류 값의 합을 위함)
val wordCount = split_flatMap.map(w => (w, 1)).take(10) //키, 밸류

//val wordCount = split_flatMap.map(w => (w, 1)).reduceByKey((a,b)=>a+b).take(10)
val wordCount = split_flatMap.map(w => (w , 1) ).reduceByKey(_+_) //위 주석과 동일한 코드, 키값들의 밸류를 합산합니다.

//_._2는 _.의 2번째(value)값 기준, false: 역순정렬
wordCount.sortBy(_._2, false).take(10).foreach(println)
```
