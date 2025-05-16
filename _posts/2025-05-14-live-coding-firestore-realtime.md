---
layout: post
section-type: study-notes
has-comments: true
title: Firebase RealTime & Remote Config 연동, 라이브코테
---

<h5> 스터디 진행 일시</h5>
<blockquote>날짜 : 5월 14일 (수요일)    
시간 : 오후 4시 ~ 9시 (5시간)   
장소 : 강남 (오프라인)
</blockquote>

<br>

<h5> 🔧 금주 스터디 일정 </h5>
![plan](/img/post_img/plan1.png)

<br>
<h5> 🔧 Firebase RealTime & Remote Config </h5>
* 현재 진행 중인 스터디 프로젝트에 Firebase RealTime & Remote Config 연동
    * Firebase에 각자 프로젝트와 Git branch를 만들고 Pull Request  

![firebase1](/img/post_img/friebase.png)
<small><center> Firebase Code 리뷰 중 </center></small>

* Firebase RealTime을 왜 쓰는걸가?
    * 장애가 발생했을 때, 원격으로도 장애를 빠르게 대응할 수 있다. 
    * firebase는 프론트 영역인가? 백엔드 영역인가?
        * 누구든 장애 대응으로 해야할 순간이 올 수 있으니까, 알면 좋다.
        * firebase는 어떤 DB의 종류일까? -> NoSQL 

<br>  

<h5> 🔧 라이브코테 </h5>

* 스터디 하는 날만이라도 코딩 테스트 연습을 하자.
    * 진행 방식 -> [라이브 코테 Repo](https://github.com/MeowDevelopers/DevDocs/tree/main/LiveCodingTest)
    * 3,6,9 프로그램 만들기
        * 3,6,9가 출력될때 "짝"을 출력해주세요.
        * 3,6,9가 포함된 갯수만큼 "짝"을 출력해주세요.
        * 1 <= n <= 100000  

        ```python
        입력
        N = 40

        출력
        1 
        2 
        짝(3)
        4 
        5 
        짝(6)
        7 
        8 
        짝(9)
        10
        11 
        12 
        짝(13)
        14 
        15 
        짝(16)
        17 
        18 
        짝(19)
        20 
        21 
        22 
        짝(23)
        24 
        25 
        짝(26)
        27 
        28 
        짝(29) 
        짝(30) 
        짝(31) 
        짝(32) 
        짝짝(33) 
        짝(34)
        ```

* 라이브코테 피드백
    * 아무리 코테라고 해도 변수 명을 신경쓰자. 잘못하면 습관된다. 
    * 모르는 부분이 있으면 빠르게 물어보자. 