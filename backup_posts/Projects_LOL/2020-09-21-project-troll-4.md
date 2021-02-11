---
layout: post
title:  "[프로젝트] CouchBase 심플 구성"
subtitle:   "[프로젝트] CouchBase 심플 구성"
description : "[프로젝트] CouchBase 심플 구성"
keywords : "couchbase, couchbase docker compose"
categories: project
tags: troll
comments: true
---


> # CouchBase 
> 구성  

## Create Cluster
- m-w1-w1
- master, worker로 구분은 편의상 나타냄

## Docker Compose

```
version: '2'
networks:
    dev:
services:
  couchbase-m:
    image: couchbase:community-6.6.0
    container_name: couchbase-m
    ports:
      - 8091:8091
      - 8092:8092
      - 8093:8093
      - 8094:8094
      - 11210:11210
    networks:
      - dev

  couchbase-w1:
    image: couchbase:community-6.6.0
    container_name: couchbase-w1
    networks:
      - dev
  
  couchbase-w2:
    image: couchbase:community-6.6.0
    container_name: couchbase-w2
    networks:
      - dev
```

## Clustering
- 확인
	- `$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' couchbase-w1`
	- `$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' couchbase-w2`

- `localhost:8091`

<img src ="https://docs.couchbase.com/server/current/install/_images/welcome.png" width="500" height="300">   

- ADD SERVER
	- 위 확인 IP로 조인 - Rebalance 