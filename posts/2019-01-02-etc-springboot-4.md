---
layout: post
title:  "[Spring boot] Springboot 04_Swagger, Api문서작성"
subtitle:   "[Spring boot] Springboot 04_Swagger, Api문서작성"
description : "[Spring boot] Springboot 04_Swagger, Api문서작성"
keywords : "springboot, annotation, Swagger, Api문서작성"
categories: etc
tags: springboot
comments: true
---


## Swagger
- postman과 같은 툴로 테스트 하는 것을 좀 더 편하게 해주는 툴 이라고 생각
- 즉 지정한 URL을 HTML화면으로 확인 할 수 있고, 값을 보내고 결과를 볼 수 있음.

<img src ="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/springboot_img/Blog_Spring%20boot04_01.png?raw=true">


- pom.xml

```
    <!-- swagger -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>${swagger.version}</version>
    </dependency>
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>${swagger.version}</version>
    </dependency>
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-data-rest</artifactId>
        <version>${swagger.version}</version>
    </dependency>
```

- mainapplication.class
	- @EnableSwagger2 명시

<br>

### Swagger로 Api문서 만들기
- Swagger Annotation을 활용해서, 해당 URL이 어떠한 것인지 어떤 값을 받아야 하는지 명시 할 수 있음.
- 아래는 하나의 예시

```
/**
 * pk = idx(배송 번호)로 조회
 */
@ApiOperation(value = "배송 번호(idx)로 조회", notes = "배송번호(idx)를 통해서 배송정보를 조회.")
@GetMapping("/selectByIdx/{key}")
public ResponseEntity<Delivery> getDeliveryByKey(
		@ApiParam(value = "배송번호(idx)", required = true)
		@Min(value = 1)
		@PathVariable Integer key) {

	...생략...
}
```

<img src ="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/springboot_img/Blog_Spring%20boot04_02.png?raw=true">

- Responses의 형태도 볼 수 있다. 중간의  **Model** 부분

<img src ="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/springboot_img/Blog_Spring%20boot04_03.png?raw=true">
