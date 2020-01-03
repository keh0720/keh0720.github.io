---
title: Apache Spark
layout: post
date: 2020-01-03
description: Apache Spark 개념 정리
image : 2020-01-03-apache-spark-architecture.png
categories: ["bigdata"]
---


# 개요
---
- 아파치 스파크는 대규모 데이터 처리를 위한 통합 분석 엔진이다. 
- Matei Zaharia가 Scala로 만든 오픈소스 엔진으로 아파치 재단에 기부했다.
- 메모리 하둡이라고도 불리는데 기존 하둡이 MapReduce 작업을 디스크 기반으로 수행하기 때문에 느려지는 성능을 메모리 기반으로 옮겨 고속화 하는데서 출발했다.

# 특징
---
## Speed

![Speed](/img/2020-01-03-apache-spark-speed.png)
- 최첨단 DAG 스케쥴러와 쿼리 최적화, 물리 실행 엔진을 이용해 배치와 스트리밍 데이터 모두 고성능을 이뤘다
- 하둡 대비 최고 100배 향상

## Ease to use  

- Scala, Java, Python, R, SQL 지원
- 80개 이상의 operation 제공

## Generality

![Generality](/img/2020-01-03-apache-spark-generality.png)

- SQL과 DataFrame, 머신 러닝을 위한 MLib, GraphX와 스파크 스트리밍을 포함한 라이브러리 스택을 제공한다.
- 이를 조합하여 하나의 어플리케이션에서 사용 할 수 있다.

## Run Everywhere
- 스파크의 standalone cluster mode는 EC2, Hadoop YARN, Apache Mesos, Kubernetes과 같은 여러 환경에서 실행 할 수 있다.
- HDFS, Alluxio, Apache Cassandra, Apache HBase, Apache Hive 외 수 백 가지의 소스로부터 데이터를 읽을 수 있다.

## RDD(resilient distributed dataset)
- 머신의 클러스터에 분산되어 배포되는 데이터들의 읽기전용 다중집합을 통해 내결함성을 유지하는 것을 설계 기반으로 한다.
- 클러스터 노드에서 분할된 요소 모음으로 병렬로 동작 할 수 있다.
- Hadoop 파일시스템이나 드라이버 프로그램의 기존 스칼라 컬렉션에서 시작하여 파일을 변형하여 생성된다.
- 사용자는 스파크 메모리에 RDD를 유지하도록 요청하여 병렬작업에서 효율적으로 재사용 할 수 있다.
- RDD는 노드 장애로부터 자동으로 복구된다.

## Shared variables
- 기본적으로 스파크의 명령이 다른 노드들의 작업의 집합으로 함수를 실행 할 때 함수에 사용 된 각 변수의 복사본을 각 작업에 제공한다.
- 2가지 형태의 변수로 컴퓨팅 노드 간 정보를 공유합니다.
  - broadcast 변수 : 모든 노드의 메모리에 값을 캐시 할 수 있는 변수
  - accumulator : 카운터나 합계 같은 더하기만 가능한 변수

# 아키텍쳐
---

![Architecture](/img/2020-01-03-apache-spark-architecture.png){:width="100%" height="100%"}

## Spark Core
스파크의 기본으로 분산 작업의 dispatching, 스케쥴링, 기본 I/O 기능, RDD 추상화를 중심으로 어플리케이션 프로그래밍 인터페이스를 통해 노출시킨다.
이 인터페이스는 함수형/고차원 프로그래밍 모델을 반영한다. "driver"프로그램은 함수를 스파크에 전달하여 map, filter 또는 reduce 같은 병렬 작업을 호출하고, RDD에서 함수를 실행하고 클러스터에서 병렬로 함수의 실행을 예약합니다.

## Spark SQL
SQL은 Spark Core의 최상의 구성요소로 구조화된 데이터와 반 구조화된 데이터를 지원하는 DataFrames라는 데이터 추상화를 제공한다.
Scala, Java, Python에서 DataFrames을 조작 할 수 있는 DSL(Domain Specific Language)을 지원한다.

## Spark Streaming
Spark Streaming은 스트리밍 분석을 위해 Spark Core의 빠른 스케쥴링 능력을 사용한다.
미니 배치로 데이터를 수집하고 해당 미니 배치에 대해 RDD 변환을 수행한다.

## MLib
분산된 메모리 기반의 스파크 아키텍쳐 덕분에 Apache Mahout에서 사용하는 디스크 기반 구현 보다 9배 빠른 Spark Core위에 있는 분산된 머신 러닝 프래임워크입니다.
많은 일반적인 머신 러닝 및 통계 알고리즘이 구현되어 MLib와 함께 제공된다.

## GraphX
  
분산된 그래프 처리 프래임워크
RDD 기반이기 때문에 그래프가 변경되지 않으므로 그래프 데이터베이스와 같은 트랜잭션 방식은 물론 업데이트가 필요한 그래프에는 적합하지 않다.

# 참조
---
- [https://spark.apache.org/](https://spark.apache.org/)
- [https://spark.apache.org/docs/latest/](https://spark.apache.org/docs/latest/)
- [https://spark.apache.org/docs/latest/rdd-programming-guide.html](https://spark.apache.org/docs/latest/rdd-programming-guide.html)
- [https://en.wikipedia.org/wiki/Apache_Spark](https://en.wikipedia.org/wiki/Apache_Spark)
- [https://12bme.tistory.com/305?category=737765](https://12bme.tistory.com/305?category=737765)


