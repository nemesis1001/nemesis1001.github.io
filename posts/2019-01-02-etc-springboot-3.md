---
layout: post
title:  "[Spring boot] Springboot 03_로깅, 롬복, validation"
subtitle:   "[Spring boot] Springboot 03_로깅, 롬복, validation"
description : "[Spring boot] Springboot 03_로깅, 롬복, validation"
keywords : "springboot, annotation, 롬복, Rest Api, lombok, validation, valid, vaidated, 로깅, logback.xml, log"
categories: etc
tags: springboot
comments: true
---

> # Spring boot 03
> 로깅, 롬복, Validation   

### 로깅
- 스프링 부트는 Commons Logging을 사용
	- 개발 당시에 SLF4j가 없었기 때문이다.

- 결론: 스프링 5이후부터, Commons Logging은 SLF4j로 보내지고 최종적으로 Logback이 찍음
	- 결국 찍히는 로그는 **Logback** 으로 찍히는 것이다

<img src="/Users/LSH/Desktop/bookSummaryMD/SpingBoot_inflearn/SpringBoot_6-1.png">


### 로깅 파일로 출력하기
- application.properties 파일안에    

`logging.path=logs`

- logs 디렉토리가 현재 project 안에 생성되면서 log가 남는다.
- 기본적으로 10mb까지 로그가 남는다.
- 로그사이즈 등에 따른 설정은 변경이 가능하다.
[Log관련 공식문서 바로가기](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/html/boot-features-logging.html#boot-features-logging-file-output)


### Logback
- spring boot에서 로그 설정하기(콘솔)
- 동작방식
	- spring boot는 logback을 사용하며, 애플리케이션을 실행하면서 spring boot는 logback.xml을 검색한다.
	- logback.xml에서 로그가 남을 수 있도록 설정한다.

[logback.xml 설정 참고하기, 정아마추어 JEONG_AMATEUR 님](https://jeong-pro.tistory.com/154)

<br>

## Lombok
- @Getter / @Setter
- @Data
	- @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor

`@RequiredArgsConstructor 어노테이션은 final이나 @NonNull인 필드 값만 파라미터로 받는 생성자를 만든다. 아래는 예시`

```
@RequiredArgsConstructor
public class User {
	private Long id;

	@NonNull
	private String username;

	@NonNull
	private Stromg password;
}

username; password 필드만 가진 생성자를 만들어줌. 예시에는 없지만 final을 붙인 필드도 추가.
```

- @AllArgsConstructor(모든 인자를 가진 생성자)
- @NoArgsConstructor(기본 생성자)
- @RequiredArgsConstructor(필수 인자만 있는 생성자)
- @Builder: 다수의 필드를 가지는 클래스의 경우, 생성자 대신에 빌더를 사용하는 경우에 사용.(빌더를 자동 추가)
	- 단점: 인자가 추가되는 일이 발생하면 코드를 수정하기 어렵다.

```
@Builder
public class User {
    private String name;
    private int id;
    private List<Integer> score;
}

// 사용
User user = User.builder().name("ABC").id(12).score(10).score(20);
```

- @NonNull : 변수에 붙이면 Null을 체크해줌. null값이 넘어오면 NullPoinerException 예외 발생

<br>

## Validation
- 어노테이션을 이용해서 bean 유효성을 검사

### Entity
- @NotNull (null 체크)
- @NotEmpty (null, 0 체크, String Collection)
- @Positive (양수 체크)
- @Negative (음수 체크)
- @Min, @Max
- @Size (String 범위)
- @Email (e-mail 형식 체크) 등이 있다.

```
@NotNull @Size(min = 5, max = 100)
private String deliveryAddress;

@Min(1) @Max(6)
private Integer deliveryKey;
```

### Controller
- class 레벨에서 @Validated
- RestApi Method의 parameter에서 @Valid

```
@Validated
public class DeliveryController {

	//String key를 Min 값 검증위해서 @Valiedated 사용
	//Entity인 delivery 검증을 위해서 @Valid 사용
	public ResponseEntity updateDelivery(
		@Min(value = 1) String key,
		@Valid @RequestBody Delivery delivery) {

			...생략...
		}
}
```
