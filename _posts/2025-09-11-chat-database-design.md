---
layout: post
section-type: study-notes
has-comments: true
date: 2025-09-11 00:00:00 +0900
title: 채팅 데이터베이스 테이블 설계
---

<h5> 스터디 진행 일시</h5>
<blockquote>날짜 : 9월 11일 (목요일)    
시간 : 오후 7시 ~ 9시 (2시간)   
장소 : 강남 (오프라인)
</blockquote>

<br>

<h5> 🔧 금주 스터디 일정 </h5>
- 아이스 브레이킹
- 채팅 DB 테이블 설계

<br>  

<h4> 채팅 테이블 설계 </h4>

채팅 테이블 설계하는 시간을 가졌습니다.   
은지님이 만든 1차 API를 기반으로, 유경님이 이번에 테이블 설계 하셨고, 다같이 검증하는 시간을 가졌습니다.

<br>
![데이터베이스-설계](/img/post_img/채팅-데이터베이스-설계.jpeg)
<br>
<small><center> DB 테이블 설계 </center></small>


<br>  


<h5> 어떤 요구사항을 고려하며 설계해야할까? </h5>
* 사용자는 회원가입과 로그인이 가능해야 한다. 
* 로그인 상태를 유지하기 위한 리프레시 토큰이 필요하다.
* 친구 삭제 / 이름 변경 기능이 있어야 한다.
* 유저들은 1:1 대화방과 그룹 채팅방에서 대화할 수 있다.
* 그룹 채팅방을 만든 사람은 역활(방장)를 맡는다. 
* 1:1 채팅에서 A 유저가 채팅방을 나갔을 경우, B 유저는 이전 대화 기록을 가지고 있고 A 유저는 이전 대화 기록이 없어야 한다.


<br>  


<h5> 유저 테이블 </h5>  

```sql
DROP TABLE IF EXISTS user;
CREATE TABLE user
(
    user_id              BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '유저 ID',
    user_email           VARCHAR(100) NOT NULL UNIQUE COMMENT '카카오 이메일',
    user_name            VARCHAR(30) NOT NULL COMMENT '카카오 이름',
    provider_type        ENUM('KAKAO') NOT NULL COMMENT '로그인 타입(KAKAO)',
    provider_id          VARCHAR(255) NULL COMMENT 'OAuth2 유저 고유 식별자',
    dormancy             BOOLEAN NOT NULL DEFAULT false COMMENT '휴면 계정 여부',
    
    created_at           DATETIME(6) NOT NULL COMMENT '생성일',
    modified_at          DATETIME(6) NOT NULL COMMENT '수정일',
    deleted_at           DATETIME(6) NOT NULL COMMENT '삭제일',
    
    UNIQUE KEY           uk_provider (provider_type, provider_id),
    INDEX                idx_email (user_email) COMMENT '카카오 이메일 index',
    INDEX                idx_name (user_name) COMMENT '카카오 이름 index'
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '유저 테이블';
```  

* 유저의 기본정보를 담고 있는 테이블이다. 
    * provider_type + provider_id를 묶어서 고유 제약 조건을 걸었다.
    * 현재는 카카오 로그인만 지원하려고 한다. 향후 다른 소셜 로그인(구글, 네이버 등)을 추가할 때 provider_type을 확장할 수 있도록 ENUM으로 설계했다.
    * dormancy 필드는 장기간 로그인하지 않은 사용자를 휴면 계정으로 처리하기 위한 플래그다. 휴면 계정은 일정 기간 후 자동으로 삭제되거나 별도 관리가 가능하다.
    * 모든 테이블에 created_at, modified_at, deleted_at을 추가했다.  

<br>  


<h5> RefreshToken 테이블 </h5>    


```sql  
DROP TABLE IF EXISTS refresh_token;
CREATE TABLE refresh_token
(
    refresh_token_id     BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT 'RefreshToken ID',
    user_id              BIGINT UNSIGNED NOT NULL UNIQUE COMMENT '유저 ID',
    refresh_token        VARCHAR(255) NULL COMMENT 'RefreshToken',
    
    created_at           DATETIME(6) NOT NULL COMMENT '생성일',
    modified_at          DATETIME(6) NOT NULL COMMENT '수정일',
    deleted_at           DATETIME(6) NOT NULL COMMENT '삭제일',
    
    INDEX                idx_user_id (user_id) COMMENT '유저 ID index'
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = 'RefreshToken 테이블';
```

* 로그인 보안을 위해 access token은 짧게, refresh token은 길게 가져간다. 

<br>  


<h5> 유저 카카오 프로필 테이블   </h5>
  
```sql
DROP TABLE IF EXISTS user_profile;
CREATE TABLE user_profile
(
    user_profile_id      BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '카카오 프로필 이미지 ID',
    user_id              BIGINT UNSIGNED NOT NULL UNIQUE COMMENT '유저 ID',
    user_profile_url     VARCHAR(500) NOT NULL COMMENT '프로필 이미지 경로',
    
    created_at           DATETIME(6) NOT NULL COMMENT '생성일',
    modified_at          DATETIME(6) NOT NULL COMMENT '수정일',
    deleted_at           DATETIME(6) NOT NULL COMMENT '삭제일'
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '유저 카카오 프로필 테이블';
```

* 카카오 프로필 이미지 정보를 별도 테이블로 분리했다.
    * 자주 수정되는 부분이기 때문에 별도로 분리 했다. 
* 프로필 이미지 캐싱이나 CDN 사용 시 이 URL을 기반으로 캐시 키를 생성할 수 있다.

<br>  


<h5> 친구 테이블 </h5>

```sql
DROP TABLE IF EXISTS friend;
CREATE TABLE friend
(
    friend_id          BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '친구 ID',
    user_id            BIGINT UNSIGNED NOT NULL COMMENT '유저 ID',
    friend_user_id     BIGINT UNSIGNED NOT NULL COMMENT '상대방 유저 ID',
    
    created_at         DATETIME(6) NOT NULL COMMENT '생성일',
    modified_at        DATETIME(6) NOT NULL COMMENT '수정일',
    deleted_at         DATETIME(6) NOT NULL COMMENT '삭제일',
    
    UNIQUE KEY uniq_friend (user_id, friend_user_id)
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '친구 테이블';
```  

* 친구 관계를 관리하는 테이블이다.
* user_id와 friend_user_id의 조합으로 친구 관계를 표현했다. 
* 친구 삭제 시 deleted_at을 설정하여 소프트 딜리트로 처리한다. 이렇게 하면 친구 관계 복구가 가능하다.

<br>  


<h5> 채팅방 테이블</h5>

```sql
DROP TABLE IF EXISTS room;
CREATE TABLE room
(
    room_id            BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '채팅방 ID',
    room_type          ENUM('DIRECT', 'GROUP') NOT NULL COMMENT '채팅방 타입(1:1, 그룹)',
    room_name          VARCHAR(100) NOT NULL COMMENT '채팅방 이름',
    room_owner_user_id BIGINT UNSIGNED NOT NULL COMMENT '채팅방 생성자 ID',

    created_at         DATETIME(6) NOT NULL COMMENT '생성일',
    modified_at        DATETIME(6) NOT NULL COMMENT '수정일',
    deleted_at         DATETIME(6) NOT NULL COMMENT '삭제일'
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '채팅방 테이블';
```

* 채팅방의 기본 정보를 담고 있는 테이블이다.
* room_type을 ENUM으로 설정하여 1:1 채팅(DIRECT)과 그룹 채팅(GROUP)을 구분한다.
* room_owner_user_id는 그룹 채팅방의 방장을 의미한다. 1:1 채팅방에서는 두 사용자 모두가 방장 역할을 한다.
* 그룹 채팅방의 경우 방장이 방을 삭제하거나 다른 사용자를 내보낼 수 있는 권한을 가진다.
* room_name은 1:1 채팅방에서는 상대방 이름으로, 그룹 채팅방에서는 사용자가 설정한 방 이름으로 표시된다.

<br>  


<h5> 채팅방 참여 유저 테이블 </h5>

```sql
DROP TABLE IF EXISTS room_users;
CREATE TABLE room_users
(
    room_users_id        BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '채팅방 참여 ID',
    room_id              BIGINT UNSIGNED NOT NULL COMMENT '채팅방 ID',
    user_id              BIGINT UNSIGNED NOT NULL COMMENT '사용자 ID',
    is_active            BOOLEAN NULL DEFAULT TRUE COMMENT '채팅방 활성화 여부',
    last_read_message_id BIGINT UNSIGNED NULL COMMENT '마지막 읽은 메시지 ID',

		created_at           DATETIME(6) NOT NULL COMMENT '생성일',
		modified_at          DATETIME(6) NOT NULL COMMENT '수정일',
		deleted_at           DATETIME(6) NOT NULL COMMENT '삭제일',
		
		UNIQUE KEY           uniq_room_users (room_id, user_id),
		INDEX                idx_user_id (user_id) COMMENT '유저 ID index'
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '채팅방 참여 사용자 테이블';
```

* 채팅방에 참여한 사용자들의 정보를 관리하는 테이블이다.
* is_active 필드로 사용자가 채팅방을 나갔는지 여부를 관리한다. false가 되면 해당 사용자는 채팅방에서 나간 상태다.
* last_read_message_id는 사용자가 마지막으로 읽은 메시지의 ID를 저장한다. 이를 통해 읽지 않은 메시지 수를 계산할 수 있다.
* 1:1 채팅에서 한 사용자가 나가도 상대방은 이전 대화 기록을 볼 수 있도록 is_active만 false로 설정한다.
* UNIQUE KEY uniq_room_users (room_id, user_id)로 한 사용자가 같은 채팅방에 중복 참여하는 것을 방지한다.

<br>  


<h5> 1:1 채팅방 테이블 </h5>


```sql
DROP TABLE IF EXISTS direct_room;
CREATE TABLE direct_room
(
    direct_room_id     BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '1:1 채팅방 테이블 ID',
    room_id            BIGINT UNSIGNED NOT NULL COMMENT '채팅방 ID',
    user1_id           BIGINT UNSIGNED NOT NULL COMMENT '채팅방 참여 유저1 ID',
    user2_id           BIGINT UNSIGNED NOT NULL COMMENT '채팅방 참여 유저2 ID',
    
    created_at         DATETIME(6) NOT NULL COMMENT '생성일',
		modified_at        DATETIME(6) NOT NULL COMMENT '수정일',
		deleted_at         DATETIME(6) NOT NULL COMMENT '삭제일',
    
    UNIQUE KEY uniq_direct_room_pair (LEAST(user1_id, user2_id), GREATEST(user1_id, user2_id))
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '1:1 채팅방 테이블';
```  

* 1:1 채팅방의 특별한 정보를 관리하는 테이블이다.
* user1_id와 user2_id로 1:1 채팅 참여자를 명시적으로 저장한다.
* UNIQUE KEY uniq_direct_room_pair (LEAST(user1_id, user2_id), GREATEST(user1_id, user2_id))로 중복 방 생성을 방지한다.
* 1:1 채팅방은 별도 테이블로 분리하여 그룹 채팅방과 다른 로직을 적용할 수 있도록 했다.
* 채팅방 나가기 시 한 사용자만 나가고 다른 사용자는 대화 기록을 유지할 수 있는 구조다.

<br>  


<h5> 채팅방 메세지 테이블 </h5>


```sql
DROP TABLE IF EXISTS message;
CREATE TABLE message
(
    message_id         BIGINT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '메세지 ID',
    room_id            BIGINT UNSIGNED NOT NULL COMMENT '채팅방 ID',
    sender_id          BIGINT UNSIGNED NOT NULL COMMENT '보낸 유저 ID',
    message_type       ENUM('TEXT', 'IMAGE', 'VIDEO', 'FILE') NOT NULL COMMENT '메세지 타입',
    message_text       TEXT NULL COMMENT '메세지 내용',
    message_url        VARCHAR(500) NULL COMMENT '메세지 파일 경로',

		created_at         DATETIME(6) NOT NULL COMMENT '생성일',
		modified_at        DATETIME(6) NOT NULL COMMENT '수정일',
		deleted_at         DATETIME(6) NOT NULL COMMENT '삭제일',
		
		INDEX idx_room_created_at (room_id, created_at)
) ENGINE = InnoDB
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci
  COMMENT = '채팅 메세지 테이블';
```

* 채팅방의 모든 메시지를 저장하는 테이블이다.
* message_type을 ENUM으로 설정하여 텍스트, 이미지, 비디오, 파일 등 다양한 메시지 타입을 지원한다.
* message_text는 텍스트 메시지의 내용을, message_url은 이미지나 파일의 경로를 저장한다.
* TEXT 타입을 사용하여 긴 메시지도 저장할 수 있도록 했다.

<br>  

