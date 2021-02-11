---
layout: post
title:  "[Spring boot] QueryDsl 공식문서 살짝 보기"
subtitle:   "[Spring boot] QueryDsl 공식문서 살짝 보기"
description : "[Spring boot] QueryDsl 공식문서 살짝 보기"
keywords : "springboot, Spring Boot Test, msa, QueryDsl, 쿼리, jpa"
categories: etc
tags: springboot
comments: true
---


> ## QueryDsl 익숙해지기  
> - Java doc 아래 부분 참고  
>	- com.querydsl.core.Query  
>	- com.querydsl.core.Fetchable  
>	- com.querydsl.core.types.Expression  


- QueryDSL 공식문서 번역 이해
- http://www.querydsl.com/static/querydsl/4.1.4/reference/html/ch02.html

## 2.1.5. Using query types

```
@Entity
public class Customer {
    private String firstName;
    private String lastName;

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setFirstName(String fn) {
        firstName = fn;
    }

    public void setLastName(String ln)[
        lastName = ln;
    }
}
```

- Querydsl will generate a query type with the simple name QCustomer into the same package as Customer. QCustomer can be used as a statically typed variable in Querydsl queries as a representative for the Customer type.


`QCustomer customer = QCustomer.customer;` or `QCustomer customer = new QCustomer("myCustomer");`  


## 2.1.6. Querying
- Both JPAQuery and HibernateQuery implement the JPQLQuery interface.
- For the examples of this chapter the queries are created via a JPAQueryFactory instance. JPAQueryFactory should be the preferred option to obtain JPAQuery instances.

```
JPAQueryFactory queryFactory = new JPAQueryFactory(entityManager);
QBoard qBoard = QBoard.board;

return queryFactory
		.selectFrom(qBoard)
		.where(qBoard.boardType.eq(boardType));

//return Type -> JPAQuery<Board>
```

## 2.1.8. General usage

```
Use the the cascading methods of the JPQLQuery interface like this

select: Set the projection of the query. (Not necessary if created via query factory)

from: Add the query sources here.

innerJoin, join, leftJoin, rightJoin, on: Add join elements using these constructs. For the join methods the first argument is the join source and the second the target (alias).

where: Add query filters, either in varargs form separated via commas or cascaded via the and-operator.

groupBy: Add group by arguments in varargs form.

having: Add having filters of the "group by" grouping as an varags array of Predicate expressions.

orderBy: Add ordering of the result as an varargs array of order expressions. Use asc() and desc() on numeric, string and other comparable expression to access the OrderSpecifier instances.

limit, offset, restrict: Set the paging of the result. Limit for max results, offset for skipping rows and restrict for defining both in one call.
```
