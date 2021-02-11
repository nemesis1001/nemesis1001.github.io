---
layout: post
title:  "[Spring boot] Circuit Breaker"
subtitle:   "[Spring boot] Circuit Breaker"
description : "[Spring boot] Circuit Breaker"
keywords : "springboot, Spring Boot Actuator, Circuit Breaker, 서킷브레이커, msa, 장애대응"
categories: etc
tags: springboot
comments: true
---

## Circuit Breaker
- Feign Client는 현재 예외상황이 없다고 가정 개발 그러므로!
	- Circuit Breaker를 통해서 예외상황에 대비
	- MSA개발에서 서비스 간에 연쇄 문제 발생을 차단하는 것은 중요한 부분.

### Circuit Breaker 패턴
- 클라이언트  > Ciruit Breaker > 서비스
	- time out되는 상황에 Circuit Breaker 를 열어서 차단.
	- open: 차단
	- close: 정상 작동

### Circuit Breaker 장애 연쇄 방지
- 장애가 난 supplier서버에 클라이언트가 요청을 하는 상황
- 클라이언트는 TimeOut이 발생할 때까지 응답이 밀려 전체적인 장애가 발생할 수 있음
- 즉, 장애를 차단하기 위해서 씀
	- 서비스 하나가 죽으면 Circuit Breaker의 조건설정에 맞춰서 미 응답시 Fallback을 던짐
	- Fallback: 임으로 정의된 값
	- **실제 사용은 FallbackFactory<>**

<br>

## Circuit Breaker의 Hystrix
- 넷플릭스 구현해서 오픈소스로 제공  = Hystrix(Ciruit Breaker의 구현 라이브러리)
	- (1) Feign Client 사용
	- (2) 일반 서비스에서 사용

### Hystrix
- spring-cloud의 서비스 중 하나로 넷플릭스에서 구현해서 오픈소스로 제공
- supplier 서버가 장애시에 일정 시간(time window)중 특정한 오류 횟수와 조건에 걸리는 경우, 다른 서버의 서비스(MSA 이라면, 다른 모듈)의 API의 응답을 기다리지 않는다.
- 그리고 정의한 fallback 함수를 실행하는 것.   

<br>

## Hystrix를 Feign Client에서 사용하기

### (1)pom.xml 에 dependency 추가

```
<!-- circuit breaker -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

### (2)application.yml에 추가

```
# Feign Client
feign:
  hystrix:
    enabled: true
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: basic


# Circuit Breaker
hystrix:
  threadpool:
    default:
      maximumSize: 10
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 1000
      metrics:
        rollingStatus:
          timeInMilliseconds: 10000
      circuitBreaker:
        requestVolumeThreshold: 5
        errorThresholdPercentage: 50


# Actuator # 아래의 설정에서 추가로 server port(GUI 접속용 port)가 들어갈 수 있음.
management:
  endpoints:
    web:
      exposure:
        include: ["health", "info", "hystrix.stream"]     
```

- `feign.hystrix.enabled: true` feignClient에서 hystrix를 사용하겠다
- `feign.client.config.default` feignClient의 설정을 하겠다
- `feign.client.config.default.connectTimeout: 5000` FeignClient 전역 연결 타임아웃 5초
- `feign.client.config.default.readTimeout: 5000` FeignClient 전역 리드 타임아웃 5초
- `feign.client.config.default.loggerLevel: Basic` loggerLevel로 BASIC을 사용하겠다.
	- NONE: 로그를 남기지 않는다. default 값
	- BASIC: Request Method, URL과 응답 코드, 실행 시간을 남김
	- HEADERS: `BASIC 정보` + Request/Response 헤더정보
	- FULL: `HEADERS 정보` + body, metadata 정보

- `중요한 점은 FeignClient의 설정보다 Hystrix 설정이 우선시 됨`

- `hystrix.threadpool.default.maximumSize: 10` ThreadPool 크기(기본 값 10)
	- hystrix는 쓰레드로 관리(쓰레드 풀에서 사용).
    - 사실은 Semaphore 방식(각 서비스 동시 호출 수량을 제한)과 쓰레드 풀 방식이 있음.
  - 현재 난 hystrix를 쓰레드풀로 사용하고 있고, 먼저 Spring boot는 기본적으로 HikariPool을 사용함.
    - 톰캣의 스레드 풀과 서비스에 대한 호출 스레드 풀이 서로 다름(격리)
	- 쓰레드풀의 크기를 관리하면서 성능저하에 대비할 수도 있음.
    - 쓰레드는 메모리영역에서 생성되고 관리되지만, 생성 수거 비용이 크므로, 쓰레드 풀도 과잉은 좋지 않다.
  - [쓰레드풀 참고하기](https://m.blog.naver.com/PostView.nhn?blogId=mals93&logNo=220743747346&proxyReferer=https%3A%2F%2Fwww.google.com%2F)

- `hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds: 1000`
	- 1초 이상 지연될 경우 fallback 실행. 기본 값은 1000

- `hystrix.command.default.metrics.rollingStatus.timeInMilliseconds: 10000`
	- 오류 감시 시간. 기본값은 10000

- `hystrix.command.default.circuitBreaker.requestVolumeThreshold: 5`
	- 감시 시간 내 요청 수 (기본값 20)
- `hystrix.command.default.errorThresholdPercentage: 50`
	- 요청 대비 오류율 (기본값 50)

- 10초동안 최소한 5번의 요청이 있어야 하고, 50프로가 실패(3번)시 circuit open(circuit breaker 작동). 이런 값을 임의로 조정을 해서 필요한 적정값을 찾아서 셋팅해야함. **감시 시간 내에 요청이 기준 횟수 이상 있어야 함. 즉 위의 조건에서 10초 동안 4번의 요청에 실패가 이뤄지면 Circuit Breaker는 작동하지 않음**


- Actuator는 circuit breaker GUI(모니터링 툴)을 위해서 셋팅하는 config
	- [Actuator 설정부분 참고하기](https://twowinsh87.github.io/etc/2019/01/17/etc-springboot-actuator/)

### (3)mainapplication.class에 어노테이션 추가

```
@EnableCricuitBreaker
public class mainApplication {

}
```

- @EnableCricuitBreaker
	- 모니터링에 필요한 부분으로, /actuator/hystrix.stream을 가능하게 함

### (4)FallbackFactory 설정
- circuit breaker open시 응답하는 것

```
@FeignClient(valu = "a", url = "${application.yml 설정값}", fallbackFactory = xxxProxyFallbackFactory.class)
public interface xxxProxy {

}
```

```
@Component
public class xxxProxyFallbackFactory implements FallbackFactory<xxxProxy> {

	@Override
	public xxxProxy create(Throwable cause) {
		return new xxxProxyFallback(cause);
	}

}
```

- @Component로 실제  사용 할 bean으로 등록
- implements FallbackFactory<T> 에서 create메소드를 오버라이드해서 구현해주면된다.

```
@Sl4fj
public class xxxProcyFallback implements xxxProxy {

	private final Throwable cause;

	public xxxProxyFallbaxk(Throwable casue) {
		this.cause = cause;
	}

	@Override
	//proxy에서 호출하는 메소드를 오버라이딩해서 구현함 ex)
	public xxx find(String id) {

		log.error(cause.getMessage());
		return null;
	}

}
```

- @FeignClient에서 FallbackFactory가 아닌 Fallback을 사용이 가능하다.
	 - 단, Fallback의 경우에는 어떤 프록시가 문제를 일으키는지 확인하기 힘들다.

참고: https://supawer0728.github.io/2018/03/11/Spring-Cloud-Feign/
