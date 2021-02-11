---
layout: post
title:  "Spark 설치하기"
subtitle:   "Spark 설치, homebrew로 설치한 Scala 버전 변경(2.11.x)"
categories: data
tags: fcdes
comments: true
---

## Spark 설치 & Scala 버전 & Homebrew 설치하기

> 개발 환경(spark, hadoop, scala가 제멋대로 설치가 되어있습니다.)<br>
 OS: Mac os Sierra 10.12.6 <br>
 Local PC Hadoop version: 3.0 <br>
 Spark version: 2.3 <br>
 scala version: 2.12.x

 <br>

 > 왜 재설치와 셋팅을 하는가? Spark에서 공식적으로 지원하는 Scala 버전은 2.11

- 그래서 문제점 <br>
 Spark runs on Java 8+, Python 2.7+/3.4+ and R 3.1+. For the Scala API, Spark 2.3.0 uses Scala 2.11. You will need to use a compatible Scala version (2.11.x).

- 해결방법:
(나의 생각)  scala 2.11.x 재설치

Spark를 설치하는데 하둡은 필요없지만, HDFS가 설치되어 있다면 버전을 맞추어주어야 한다.

1. Hadoop version 확인 (Spark 설치시 버전을 맞추기 위해서)<br>
`$ hadoop version`

2. Hive version 확인 (Spark 설치시 버전을 맞추기 위해서)<br>
`$ hive --version`

3. Java version 확인 (Spark 사용을 위해서, Java 미 설치시 1.8 이상을 설치하세요)<br>
`$ java -version`

4. Scala version 확인(저는 스칼라를 사용합니다. Python, Java, Scala 각자 기호에 맞게 설치하세요)<br>
`$ scala -version`

5. spark version 확인<br>
`$ spark-shell` <br>
scala> sc.version <br>
res0: String = 2.3.0

**본격적인 해결: homebrew로  설치한 scala를 만져보자...**

- homebrew로 설치한 list 확인 <br>
`$ brew list₩`

- homebrew 지원 패키지 버전 확인해봄 <br>
참고: brew search [패키지명] <br>
`$ brew search scala`

```
scala ✔       scala@2.11   scala@2.10    scalaenv      scalapack     scalariform   scalastyle
(무언가 Scala 버전 패키지들 같음. 그냥 Scala는 최신 버전 패키지인 것 같고...일단 Scala 최신버전 깔려있긴함)
```

<br>
---
<br>

1. Scala 2.11 재설치 및 연결(여기서 부터는 지극히 제가 막 설치하고 했음...)<br>
먼저 요렇게 해봄. 오류났음 안됨 <br>
`$ brew install homebrew/versions/scala211`<br>
Error: homebrew/versions was deprecated. This tap is now empty as all its formulae were migrated. 오류났음

2. 2.11을 설치해보자(설치 되었음)<br>
`$ brew install scala@2.11`

3. 기존 Scala와 링크를 끊고 <br>
`$ brew unlink scala`

4. 이렇게 해봄<br>
`$ brew link scala@2.11 --force`<br>
`$ scala -version`<br>
Scala code runner version 2.11.12 -- Copyright 2002-2017, LAMP/EPFL

<br>
**뭔가 연결 된 것 같음! 만약 환경 설정문제가 있다면 인터넷을 참고합시다**
<br>
<br>


참고: hombrew 명령어

-	homewbrew 원하는 리스트 삭제하기<br>
 brew uninstall [삭제할 리스트명]<br>
`$ brew uninstall scala`

- 리스트 중 업데이트 가능한 것 확인<br>
`$ brew outdated`

- 패키지 업데이트하기<br>
`$ brew update [패키지명]`

- homebrew 업데이트<br>
`$ brew update`

<br>
**Spark 설치 이전에 Java8이상, Scala2.11(Spark 공식지원 버전) 설치가 되어있다고 가정합니다**
