---
layout: post
title:  "[RDBMS]MySQL 사용하기"
subtitle:   "[RDBMS]MySQL 사용하기"
description : "RDBMS MySQL 사용하기"
keywords : "rdbms, mysql, sequelpro, rdbgui, 데이터베이스, rdb"
categories: etc
tags: rdbms_mysql
comments: true
---


># [RDBMS]MySQL 사용하기
> 개인적으로 HDFS에 중점을 두고 공부했으나,  
> 그래도 가장 많이 쓰이는(목적에따라) RDBMS에 대해서 오랜만에 정리하는 시간을 가져보는 목적  
> (1)Mysql이란?  
> (2)터미널에서 사용해보기  
> (3)sequel pro를 이용해서 gui로 편하게 사용하기  


### MySQL이란?
- 오픈소스다. 사용자는 명령을 통해 데이터베이스에 처리할 내용을 전달해야함. 이 때 명령을 문자로 하는 것으로 질의(query)
- 주로 나의 경우에는 불변하는 정형데이터를 매핑의 용도로 사용.
- DM을 구성할 때도 사용함. 대부분의 BI툴에서 지원하는 것도 큰 장점

<br>


### 설치하기
- [MySQL 다운로드](http://dev.mysql.com/downloads/mysql/)
- 체크하기 **Automatically Start MySQL Server on Startop**  

<br>

### 터미널에서 실행하기
- $ cd /usr/local/mysql/bin  
- /usr/local/mysql/bin$ sudo ./mysql -u root

<br>

### 데이터베이스 다루어보기
- 데이터베이스 만들기: mysql> create database 데이터베이스명;

- 데이터베이스 목록 표시: show databases;

- 사용할 데이터 베이스 지정하기: use 데이터베이스_이름 ex) use ex1

- 현재 데이터 베이스 표시하기: select database();

- 테이블 만들기: create table tb(number vachar(10), age int)

- 테이블 복사하기: create table tb1 select * from tb;
	- 기존 테이블명이 tb, 복사해서 만들 테이블명이 tb1
	- or  create table tb1 select (컬럼명) from tb


### 데이터 베이스 다루기(기본 명령어 편. DML)
- Select / Insert / Update / Delete

- select 컬럼이름들 from 테이블명;
	- 컬럼 값들이 조회가 된다.
- insert into 테이블명 (컬럼이름, 컬럼이름...) values(데이터1, 데이터2…),(데이터1, 데이터2…) ;
	- 테이블 스키마에 따라서 Null값 허용유무, PK 주의해서 넣읍시다


> 테이블을 만들면서 스키마를 지정하는 것이 참으로 번거롭기도 하고... 편한 GUI 툴을 사용해보자  



> 참고하기
> homebrew 설치시 [참고하기](https://github.com/helloheesu/SecretlyGreatly/wiki/%EB%A7%A5%EC%97%90%EC%84%9C-mysql-%EC%84%A4%EC%B9%98-%ED%9B%84-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)  
