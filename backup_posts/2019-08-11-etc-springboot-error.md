---
layout: post
title:  "[Spring boot] Errors"
subtitle:   "[Spring boot] Errors"
description : "[Spring boot] Errors"
keywords : "springboot, dataSource, jdbc, error, Error creating bean with name 'dataSource', Error creating bean with name 'pagedResourcesAssembler' defined in class path resource"
categories: etc
tags: springboot
comments: true  
---  

> 스프링 boot를 사용하면서 발생하는 에러들을 정리  

## Spring boot Errors

### # Error creating bean with name 'dataSource'

- DB 관련 pom.xml에서는 jdbc를 설정하고, dataSource 즉 application.yml 등에서 DB 정보를 입력을 하지 않았거나 할 때, 발생.

<br>

### # Error creating bean with name 'pagedResourcesAssembler' defined in class path resource

- JPA Repository interface 쓰면서 타입을 제대로 쓰지 않았거나
- public interface TestRepository extends JpaRepository<T>, QuerydslPredicateExecutor<T> 에서 QuerydslPredicateExecutor을 동시에 사용하고자 할 때. 아래처럼 해주자

- Test Entity의 PK가 1개이고, Type이 String인 경우

```
//TestEntity 클래스
@Id
private String testId;

private Long testNo;


//TestPlaceRepository 인터페이스
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.querydsl.QuerydslPredicateExecutor;

import java.util.List;

public interface TestPlaceRepository extends JpaRepository<TestEntity, Long>, QuerydslPredicateExecutor<TestEntity> {
}
```

- Test Entity의 PK가 2개 이상의 복합키의 경우

```
//TestPk 클래스
@Data
@AllArgsConstructor
@NoArgsConstructor
public class TestPk implements Serializable {

	private Long Id;
	private TestType type;
}

//TestPlaceRepository 인터페이스
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.querydsl.QuerydslPredicateExecutor;

import java.util.List;

public interface TestPlaceRepository extends JpaRepository<TestEntity, TestPk>, QuerydslPredicateExecutor<TestEntity>
```
