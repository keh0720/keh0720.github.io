---
title: Software Architecture
layout: post
date: 2019-12-19
description: Software Architecture의 정의
image: cnc-module-view.jpg
categories: ["SoftwareEngineering"]
---

우리팀에서는 아래와 같은 구분으로 문서화를 하는데 특히 module view와 C&C view의 개념이 햇갈려 이에 대해 정리해본다.

* Overview
* Context diagram
* Module view
* C&C view
* Allocation view

# Module view
* * *
- module은 software의 구현단위
- 소스 코드의 청사진으로 사용 가능
- 소스코드 또는 디렉토리와 같은 물리적 구조와 대응
- 요구사항에 대한 추적 용이성
- 영향도 분석
- 다양한 표현 방법: 비정형, UML, ER diagram 등
- 다른 뷰에 필요한 정보가 과도하게 포함 될 수 있음
- | 분류        | 설명              |
|:-------------|:------------------|
| 분할 스타일(Decomposition style) | module과 sub구조 표현. module은 다른 뷰에서 사용 가능 |
| 사용 스타일(Uses style) | module간의 기능 종속 관계 표현|
| 일반화 스타일(Generalization style) | module간의 특화 관계 표현|
| 데이터 모델 스타일(Data Model style) | 데이터 엔티티 간의 관계 |


# C&C (Component & Connector) view
* * *
- runtime에 나타나는 component들로 구성
- 선과 도형 위주의 diagram으로 표현
- component들은 프로세스, 객체, 클라이언트, 서버, 데이터저장공간을 나타냄
- connector는 통신연결, 통신규약, 정보흐름, 공유저장소 등을 나타냄
- | 분류        | 설명              |
|:-------------|:------------------|
| 호출-반환 스타일 (Call-return style) | 한 component가 다른 component가 제공하는 기능의 동기적 호출에 의해 상호작용 하는 스타일|
| 데이터 흐름 스타일 (Data flow style) | 시스템에 걸친 데이터 흐름에 의해 계산이 구동되는 스타일|
| 이벤트 기반 스타일 (Event-based style) | component들이 이벤트나 메시지에 의해 상호작용하는 스타일|
| 저장소 스타일 (Repository style) | component들이 영구적이고 공유되는 대규모 데이터의 집합체를 통해 상호작용하는 스타일|


# Allocation view
---
- Software architecture와 환경을 mapping
- Deployment style
  - software element가 어떤 물리적 장치에 탑재되는지를 나타냄
- Work assignment style
  - Module view의 내용 중 구현 책임이 있는 개인이나 그룹에 할당
  - 구조와 작업하는 개발 그룹간의 관계

# Reference
---
* [Software Architecture: 무엇을 설명할 것인가?](https://technical-leader.tistory.com/34)
* [소프트웨어 아키텍처 문서화](https://www.slideshare.net/ssuserff7918/ss-16975285)
