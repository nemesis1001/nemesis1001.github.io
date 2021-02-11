---
layout: post
title:  "[Spring boot] logstash에 로그를 보내자. logBack.xml, slf4j"
subtitle:   "[Spring boot] logstash에 로그를 보내자. logBack.xml, slf4j"
description : "[Spring boot] logstash에 로그를 보내자. logBack.xml, slf4j"
keywords : "springboot,log, logback, logback.xml, slf4j, logstash, spring, boot, elasticsearch, log, 로그, 스프링부트, 로그백"
categories: etc
tags: springboot
comments: true  
---  

> ## Spring boot 2.0에서 sl4fj와 Logback to Logstash
> logback 사용하기
> slf4j
> 회사에서 logstash-logback-encoder를 통해 log를 Elasticsearch에 전송하는데, 각종 콘솔에 찍히는 로그들을 모니터링하려고 함. 이에 맞추어서 설정하는 방법을 정리한다. 추가적으로 로깅에 대해서도 정리    

## Spring boot에서 logging

- 현재 사용하고 있는 slf4j는 spring-boot-starter-logging에 포함되어 따로 의존성 추가가 필요없다.
- 알아보니 스프링 부트 애플리케이션은 `SLF4J`와 `logback`을 기본으로 사용.
- 이외에 다른 로깅 구현체들 `Log4J` 등을 사용할 수도있고..그러나 최종적으로 `logback`용 binding을 호출해서 결국 `logback`을 사용해 로깅하게 된다는 사실..[참고](https://www.slideshare.net/whiteship/ss-47273947)

## slf4j 사용하기
- (1)Lombok 미사용시

```
Logger logger = LoggerFactory.getLogger(xxx.class)
logger.Info("로그를 남겨요")
```

- (2)Lombok 사용시

```
클래스 레벨에 @slf4j 명시

log.Info("### 사용자 번호 로그 userNo: {}", userNo); //userNo는 위에서 변수
```

```
//결과
2019-05-30 15:55:00.044  INFO[-,,,] 96239 --- [yBean_Worker-23] k.c.l.t.e.service.Service         : ### 사용자 번호 로그 userNo: 123
```

## Spring Log > Logstash 어떻게?


## logstash-logback-encoder
- logstash-logback-encoder를 통해서 Json 포맷으로 변경하자.
- ElasticSearch 의 기본 데이터 저장 구조인 Json 형태로 변경해야!
- 그리고 logstash에도 보내줘야지!

## pom.xml

```
<!-- logging -->
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>5.2</version>
</dependency>
```

## logback.xml (src/main/resources/logback.xml)
- 스프링부트는 logback을 확장기능이 가능한데 나는 아래와 같이 구성했다.
- Spring boot 애플리케이션의 로그 -> 로그스태시에 넣기위한 logback.xml 설정이다.

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<!-- logback에 대한 기본적인 설정을 base.xml을 통해서 제공함.console,file appender 를 base.xml에 include 하고 있음-->
    <include resource="org/springframework/boot/logging/logback/base.xml" />

    <!--appender는 출력을 담달하는데 정의에 따라서 통신, 이메일 파일출력등이 가능- https://hongkyu.tistory.com/76 -->
    <!--Tcp 통신하는 것 같고, 공식문서를 보니 비동기라고 함! -->
    <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
    	 <!-- 목적지로 쏴줄 것임. 값은 K8S 외부 주입때문에 저러함. host:port로 설정하길-->
        <destination>${LOGSTASH_URL:-localhost:5000}</destination>
        <!-- ES가 알아먹도록 JSON 형태로 인코딩까지 해주고-->
        <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <mdc />
                <pattern>
                		<!-- 아래와 같은 키밸류 타입이 들어가면서 나중에 키바나에서 찾기 수월함-->
                    <pattern>{"serviceID":"myServiceName"}</pattern>
                </pattern>
                <timestamp />
                <!--<version />-->
                <context />
                <threadName />
                <logLevel />
                <message />
                <loggerName />
                <logstashMarkers />
                <stackTrace />
                <callerData />
            </providers>
        </encoder>
    </appender>

	<!-- 다른 불필요 설정 생략~~~ -->

    <!--kr.co.lsoft 는 패키지명 해당 패키지는 TRACE 레벨 이상이면, CONSOLE과 LOGSTASH 설정을 참고함. -->
    <logger name="kr.co.lsoft" level="TRACE" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="LOGSTASH" />
    </logger>

    <!-- TRACE > DEBUG > INFO > WARN > ERROR, 대소문자 구분 안함 -->
    <!-- root는 전역으로 INFO 레벨 이상인 로그만 남기고 있다.-->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="LOGSTASH" />
    </root>
</configuration>

```

- 아래의 공식문서에 아주 잘 나와있다.
[logstash 공식문서 tcp-appenders](https://github.com/logstash/logstash-logback-encoder#tcp-appenders)


## 정리하자면
- Spring boot에서는 logback.xml 즉 logback의 확장을 통해서 내가 로그에 관한 커스터마이징을 할 수 있다,
- Service Application 발생 로그 -> logback.xml 에서 커스터마이징(포맷, JSON, logstash 사용 등)을 셋팅.
- logback 의 appender 설정을 통해서어플리케이션의 로그를 logstash로 전송할 수 있는 방법을 지원한다.
