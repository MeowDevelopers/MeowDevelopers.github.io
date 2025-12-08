---
layout: post
section-type: study-notes
has-comments: true
date: 2025-11-27 00:00:00 +0000
title: AI백엔드란 ? / Java Sealed에 대해 / Git에 대한 전략 및 고찰
---

<h5> 스터디 진행 일시</h5>
<blockquote>날짜 : 11월 27일 (목요일)    
시간 : 오후 7시 ~ 9시 (2시간)   
장소 : 강남 (오프라인)
오늘 함께한 멤버 ❣️ : 준호님 / 은지님  / 유경님 / 근우님  
</blockquote>

<br>  
<h5> 🔧 금주 스터디 일정 </h5>
- 아이스 브레이킹
- AI백엔드란? (은지님 세미나)
- Java Sealed에 대해 (유경님 세미나)
- Git에 대한 전략 및 고찰 (준호님 세미나)


<br>

<h4>AI백엔드는 무엇일까?</h4>
🙌 은지님의 세미나 🙌   

* 은지님이 정리하신 [노션](https://ivory-scorpio-255.notion.site/2b703c38767380cfa1c4d0db94d632d6?source=copy_link)
<br>

* AI 백엔드
    * 데이터 처리, 모델 연동, 비정형 데이터(텍스트·이미지 등) 기반 기능 제공을 담당하는 백엔드  
    * 알고리즘 실행, 모델 결과 후처리, 데이터 파이프라인 구성 등 계산 중심
    * 모델 버전 관리, 입력/출력 검증, 성능 튜닝(지연, 메모리, 비용)

* 스프링 백엔드
    * 회원·결제·주문 등 정형화된 비즈니스 로직을 처리하는 서비스 백엔드
    * 트랜잭션 중심, 도메인 규칙을 구현하는 업무 로직 중심
    * 트랜잭션 안정성, 대규모 트래픽 처리, DB 스키마 관리



<br>

<h4>Java Sealed에 대해 알아보자</h4>
🙌 유경님의 세미나 🙌  
<br>

* Sealed Class/Interface는 상속을 허용할 타입을 명확히 제한하는 기능이다. 
* Java 17에서 정식 도입되었다.
* 왜 도입되었을까? 
    * Java의 기존 상속 구조는 확장이 너무 자유롭다는 문제가 있었다.
    * 어떤 클래스가 상속 가능한지 제어하기 어려움
    * 유지보수 시 하위 타입이 어디서 갑자기 등장할 수 있음
    * API 설계 시 의도치 않은 subclass가 생겨 위험하거나 복잡도가 증가
* 그래서 Java는 **상속을 열어줄 곳만 열어준다**라는 원칙을 적용하기 위해 Sealed Class를 도입했다.
* 아래처럼 사용하면, 상속을 받을 수 있는 클래스를 명시적으로 제한한다.   

```java
public sealed class Shape permits Circle, Rectangle {}
```

<br>

<h4>Git에 대한 전략 및 고찰</h4>
🙌 준호님의 세미나 🙌  
<br>
<embed src="/img/Git.pdf" width="100%" height="800px" />

<br>


