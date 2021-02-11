---
layout: post
title:  "[Spring boot] Spring Boot Actuator"
subtitle:   "[Spring boot] Spring Boot Actuator"
description : "[Spring boot] Spring Boot Actuator"
keywords : "springboot, Spring Boot Actuator, actuator, endpoint, actuator endpoint"
categories: etc
tags: springboot
comments: true
---

## Spring Boot Actuator
- Actuator endpoint는 애플리케이션과 상호작용 및 모니터를 가능하게 함.
	- 예를 들어서 빈, 자동설정, JVM 상태 등등
- Spring Boot는 많은 내장된 엔드포인트가 있다.
- 각각의 내장된 엔드포인드를 설정에 따라서 활성화/비활성화 할 수 있다.

### Spring Boot Actuator 설정하기
- Actuator에는 상당히 민감하고 많은 정보가 노출된다.
- 그러므로 exposure.include: 값 으로 노출 할 정보만 설정 할 수 있다.
- pom.xml

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- application.yml 아래처럼 셋팅한다면?

```
# Actuator
management:
  endpoints:
    web:
      exposure:
        include: ["health", "info", "hystrix.stream"]
    health:
      show-details: always
```

- URL/actuator 로 접속
	- 내가 노출하고자 하는 정보를 확인 할 수 있음

- URl/actuator/health
	- show-details: always 시에는 상세한 정보가 나옴
- URl/actuator/info

- hystrix.stream의 경우
	- 위 처럼 기존에 내장된 health, info 이외에 이 애플리케이션에 의존하는 시스템들도 check가 가능
	- 알맞은 모니터링 툴에 대해서는 해당 의존하는 것 개별적으로 매뉴얼에서 찾아서 사용.


### Actuator endpoints
- 내장된 Endpoints 알아보러 가기  
[Strping boot Actuator endpoint 참고하기](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html)


### 노출되는 Json 정보를 어떻게 관리할까?
- (1)management.server.port, management.server.address 값을 수정해서 해당 ip, address에 한해 ACL를 걸어준다.
- (2)spring-security를 이용하여 management.endpoints.web.base-path(기본값 /actuator)에 대해 권한을 확인
- (1), (2)를 동시에 사용할 수 있음.  

참고: 스프링 부트 공식문서, https://supawer0728.github.io/2018/05/12/spring-actuator/
