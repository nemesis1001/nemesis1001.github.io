---
layout: post
title:  "[Spring boot] Springboot 01_알아보기"
subtitle:   "[Spring boot] Springboot 01_알아보기"
description : "[Spring boot] Springboot 01_알아보기"
keywords : "msa, springboot, 스프링부트, springboot프로젝트, jar배포"
categories: etc
tags: springboot
comments: true
---

> # Spring boot 01
> MSA에 관심을 가지고, 먼저 웹서버 부터 다루어보기  
> DI, AOP 등에 대한 이야기는 다루지 않습니다.     
> (1)Spring boot란?  
> (2)Spring boot 프로젝트 만들기   

### Spring boot란? 그리고 장점
- boot가 의존성 관리를 해주면서, 버전별로 충돌하는 것에 대한 문제를 해결.
- boot으로 하면 parent 구조로 관리(필요한 부분은 직접 정의)

<br>

### Spring boot 프로젝트
- File > new project > Empty project
- Modules > `+` > Spring Initializr
	- SDK 1.8
	- Group: 회사명 + 프로젝트명.
	- Artifact: 실제 서비스 네임. msa에서 하나의 서비스.
	- Version: 프로젝트 버전을 기록

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/springboot_img/blog_springboot01_01.png?raw=true">

- Dependencies에서 필요한 의존성을 추가한다.

<br>

### Spring boot 의존성 알아보기

```
(1)Spring-boot가 관리하는 것. 버젼은 명시를 안해도 알아서됨.
	<dependency>
		<groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
```

```
(2)Spring-boot가 버전관리를 안해주는 것은 직접 버전을 명시하고, 의존성을 추가한다.
	<dependency>
		<groupId>org.modelmapper</groupId>
       <artifactId>modelmapper</artifㄴactId>
       <version>2.1.0</version>
    </dependency>
```

```
(3)Spring-boot가 관리해주지만 버전을 바꾸고 싶은 경우. 스프링 버전변경 예시
<properties>
	<spring.version>5.6.RELEASE</spring.version>
</properties>
```

### 스프링부트 기타  
- http -> https, http2 로 설정하기
	- tomcat의 경우 8.5 버전에서 셋팅하기 어렵다. tomcat 9버전, jdk1.9 라면 쉽다

- jar 배포, 실행
	- 패키징: ```$ mvn clean package```
	- jar실행: ```$ java -jar target/패키지명```

- 유의할 점.
	- mvn package가 일어날 때, Main을 하고 Test부분을 package 하기 때문에
	- 동일한 application.properties가 있으면 Test가 우선시 된다(동일한 객체에 다른 값이 있다면 배포시 문제).
	- 물론 Test에 그러한 없다면 main 루트의 설정파일이 default다. 
