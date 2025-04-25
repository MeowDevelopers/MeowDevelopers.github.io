---
layout: post
section-type: study-notes
has-comments: true
title: 기본 서버 아키텍처 그리기, Node.js 기초
---

<h5> 스터디 진행 일시</h5>
<blockquote>날짜 : 4월 25일 (금요일)    
시간 : 7시 ~ 9시    
장소 : 강남(오프라인)
</blockquote>

<br>

<h5> 🔧 기본 서버 아키텍처 그리기 </h5>

![서버구조](/img/post_img/서버%20구조.png)
<small><center> 서버 아키텍처 예시 </center></small>

* 유저가 어떠한 서비스를 이용한다고 가정하고 DNS부터 서버에 어떻게 접속하는지 그려보자
    * 유저가 "naver.com"을 검색하면 ?
        * DNS에서 진짜 서버를 찾아갈 수 있는 서버 IP주소를 가져온다.  
    * 방화벽을 지나 로드밸런서에 도착 ! 그리고?  
        * 각 서버에서 작업을 진행 -> 캐시에 필요한 데이터가 있으면 캐싱, 없으면 DB 
        * 읽기 작업이라면 Read DB / 쓰기 작업이라면 Write DB  
        * 비정형 데이터라면 NoSQL  
    * NAT는 어디에 그려 넣어야 할까?  
        * 로드밸런스는 트래픽을 분산해서 서버에 넣는다.
        * NAT는 트래픽을 내보낸다.
    * 로드 밸런스의 트래픽 분산은 어떻게 이루어질까?  
        * 여러 방식이 있겠지만 우선 하나라도 외워보자.
            * 라운드로빈 -> 카드 하나씩 분배하듯 트래픽 순차적 배분 !

<br>
  
<h5> Node.js 기초 </h5>   

* 요즘 Node.js를 많이 사용하는 것 같아서 함께 공부해보자.
    * [Meow Git](https://github.com/MeowDevelopers/meow-express)에 자료 정리 후 발표 
    * 1주차 : node -> router 생성하기 -> controller 만들기
        ```
        // Express 모듈을 불러옵니다.
        // Express는 Node.js에서 가장 많이 사용되는 웹 프레임워크입니다.
        // 마치 Spring Boot에서 @RestController로 HTTP 요청을 처리하듯, Express는 경로 기반으로 핸들러를 만듭니다.
        import express from "express";
        import users from "./router/user/route"

        // Express 애플리케이션 객체를 생성합니다.
        const app = express();

        // 사용할 포트를 정의합니다.
        // 보통 개발 환경에서는 3000 포트를 많이 사용합니다.
        const PORT = 3000;


        app.use('/users', users)
        // 서버를 지정한 포트에서 실행합니다.
        // app.listen은 Spring Boot의 `@SpringBootApplication`의 실행 부분(main 메서드)과 유사합니다.
        app.listen(PORT, () => {
        console.log(`✅ Server is running at http://localhost:${PORT}`);
        });
        ```