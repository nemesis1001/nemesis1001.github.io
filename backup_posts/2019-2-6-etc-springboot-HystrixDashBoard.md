---
layout: post
title:  "[Spring boot] hystrix dashboard"
subtitle:   "[Spring boot] hystrix dashboard"
description : "[Spring boot] hystrix dashboard"
keywords : "springboot, Spring Boot Test, msa, hystrixdashboard, 히스트릭스대시보드"
categories: etc
tags: springboot
comments: true
---

> ## Hystrix Dashboard  
> - Command 실행 후 결과를 수집하고 보여준다  
> - 개인적으로 사용이유는 proxy 통신에 대해서 모니터링 하려고 사용하는 중.  

### 2가지 방법
- (1)현재 개발하고 있는 모듈(서비스)의 Hystrix을 모니터링 한다.
- (2)MSA 구축을 하면서 다른 모듈들을 하나의 대시보드에서 확인한다.
	- (2)는 유레카를 사용해야하는 문제가 있음.
	- 현재 회사에서 사용하는 방법이 있으나, 나중에 정리되면 올림

### (1)방법
- 모든 것은 fallback이 잘 적용된 상태라고 가정. [fallback 적용하러 가기](https://twowinsh87.github.io/etc/2019/01/19/etc-springboot-circuitbreaker/)

- pom.xml 추가

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- application.yml

```
# Actuator
management:
  endpoints:
    web:
      exposure:
        include: ["health", "info", "hystrix.stream"]
```

- mainServiceApplication.class

```
<추가>
@EnableCircuitBreaker
@EnableHystrixDashboard
```

- 확인하는 방법(따로 모니터링용 port를 설정하지 않았다면)
	- (1)http://localhost:{포트}/actuator/hystrix.stream 접속
	- **@EnableCircuitBreaker** 에 의해서 계속 정보를 endpoint로 보내줌.
	- (2)http://localhost:{포트}/hystrix/ 접속
	- 중간에 url에 아래처럼 넣고, MonitorStream 클릭
	- http://localhost:{포트}/actuator/hystrix.stream
	- **@EnableHystrixDashboard** 에 의해서 대시보드가 받은 정보로부터 그려짐.

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/springboot_img/hystrixdashboard.png?raw=true">

- (1)은 실제 대시보드를 보는데 반드시 할 필요는 없지만, 이렇게 정보가 보내진다는 것을 확인하기 위함
- (2)번 상태에서 실제로 테스트하거나 서비스하면서 확인할 수 있음.
