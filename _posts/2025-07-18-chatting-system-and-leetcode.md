---
layout: post
section-type: study-notes
has-comments: true
title: 채팅 시스템 설계 (1/2 진행)과 라이브 코테
---

<h5> 스터디 진행 일시</h5>
<blockquote>날짜 : 7월 18일 (금요일)    
시간 : 오후 6시 ~ 9시 (3시간)   
장소 : 강남 (오프라인)
</blockquote>

<br>

<h5> 🔧 금주 스터디 일정 </h5>
- 개발 책 12장 - 채팅 시스템 설계(1/2 진행 )
- 라이브 코테 진행  

<br>  


<h5> 📖 개발 책 12장  </h5>

- 12장
  - 11장은 채팅 시스템 설계에 관한 이야기를 다룬다.
  - 채팅 시스템을 알기 위해서는 클라이언트와 서버의 통신 방법에 대한 기본적인 지식을 갖추고 있어야 한다.
  - 우선 채팅 서비스는 아래 기능을 제공해야 한다. 
    - 클라이언트들로부터 메시지 수신
    - 메시지 수신자 결정 및 전달
    - 수신자가 접속 상태가 아닌 경우에는 접속할 때까지 해당 메시지 보관 
  - 서버가 연결을 만드는 것처럼 동작할 수 있도록 하기 위해 많은 기법이 제안되어 있다.
    - 폴링(polling)
    - 롱 폴링(long polling)
    - 웹 소켓(WebSocket)
  - 폴링은 클라이언트가 주기적으로 서버에게 새 메시지가 있느냐고 물어보는 방법이다. 폴링 비용은 자주하면 할수록 올라간다. 답해줄 메시지가 없는 경우에는 서버 자원이 불필요하게 낭비된다는 문제도 있다. 
  - 롱 폴링은 클라이언트의 새 메시지가 반환되거나 타임아웃될 때까지 연결을 유지한다. 클라이언트는 새 메시지를 받으면 기존 연결을 종료하고 서버에 새로운 요청을 보내어 모든 절차를 다시 시작한다. 

<br>


<h5> 💻 라이브 코테 </h5>  
* 문제 : [https://leetcode.com/problems/longest-common-prefix/description/](https://leetcode.com/problems/longest-common-prefix/description/)
    * 이전에는 1시간 동안 문제를 풀고 스터디를 끝냈지만, 금주부터는 다른 방식으로 진행한다.
    * 제한 시간 내에 가장 먼저 푼 사람이 어떻게 풀었는지 설명하고, 다 같이 코드 수정해서 더 나은 코드로 만들기 
    * 이번주 베스트상 : 👏👏 **@minch819님!** 👏👏👏   
    <br>
    ![firebase1](/img/post_img/코테1.png)
    <br>
<small><center> 다 같이, 통과된 민채님 코드를 수정해서 더 효율적인 코드로 만듬 </center></small>


<br>
