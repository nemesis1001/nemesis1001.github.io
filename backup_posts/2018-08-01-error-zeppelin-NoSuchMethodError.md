---
layout: post
title:  "[Zeppelin Error]java.lang.NoSuchMethodError"
subtitle:   "[Zeppelin Error]java.lang.NoSuchMethodError"
categories: data
tags: issue
comments: true
---

## 제플린 에러(java.lang.NoSuchMethodError)

1. 제플린 폴더로 이동<br>
`$ cd /conf`


2. template 활성화<br>
`~/zeppelin/conf$ cp -p zeppelin-env.sh.template zeppelin-env.sh`


3. 환경설정<br>
`$vi zeppelin-env.sh`


4. JAVA_HOME / SPARK_HOME 주석 제거하고 PATH 설정<br>
java 경로 확인 <br>
`$ /usr/libexec/java_home -v 1.8`<br>

spark 다운받은 경로 확인하기<br>
ex) # 주석 제거후<br>
`export JAVA_HOME="your Java path"`<br>
`export SPARK_HOME="your spark path"`<br>


5. vi 나와서 저장<br>
`$source zeppelin-env.sh`
