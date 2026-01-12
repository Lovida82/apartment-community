# 평택고덕행복주택 커뮤니티 앱

## 배포 파일

이 폴더에는 Netlify 배포용 파일들이 포함되어 있습니다.

### 파일 목록

1. **index.html** - 메인 커뮤니티 앱 (완전한 기능 포함)
2. **admin_page.html** - 관리자 페이지

## 🚀 배포 방법

### Netlify 드래그 앤 드롭
1. [Netlify](https://app.netlify.com) 접속
2. "Sites" → "Add new site" → "Deploy manually"
3. 이 폴더 전체를 드래그 앤 드롭

### Git 배포
```bash
git add dist/
git commit -m "Deploy community app"
git push
```

Netlify 설정에서 **Publish directory**를 `dist`로 설정하세요.

## 🔥 접속 URL

- **메인 앱**: `https://your-site.netlify.app/`
- **관리자 페이지**: `https://your-site.netlify.app/admin_page.html`

## ✨ 주요 기능

### 메인 앱 (index.html)

#### 🎯 핵심 기능
- ✅ 회원가입/로그인 (일반 사용자 + 관리자)
- ✅ 대시보드 (7개 메뉴)
- ✅ **이벤트 관리 (생성/수정/삭제/참가/취소)**
- ✅ **모임방 관리 (생성/수정/삭제/채팅)**
- ✅ 40개+ 관심사 카테고리
- ✅ 1:1 이웃 매칭
- ✅ 개인설정

#### 🆕 신규 기능 (2025-11-21)
1. **공지사항 시스템** 📢
   - 긴급공지/일반공지 구분
   - 관리자 전용 작성 기능
   - 검색 및 필터링
   - 무한스크롤 지원

2. **모임 후기 게시판** 📝
   - 게시글 작성 및 삭제
   - 댓글 시스템 (실시간 동기화)
   - 좋아요 기능
   - 관심사별 필터링
   - 검색 기능
   - 무한스크롤 지원

3. **검색 및 필터링 강화** 🔍
   - 이벤트: 검색, 관심사별/상태별 필터
   - 모임방: 검색, 관심사별/동별 필터
   - 공지사항: 검색, 유형별 필터
   - 게시판: 검색, 관심사별 필터

#### 기존 기능
4. **1:1 인앱 메시지** 💬
   - 카카오톡 없이 앱 내에서 직접 대화
   - 실시간 메시지 스레드
   - 매칭 수락 시 자동 DM 생성
   - 대화 목록 관리

5. **알림 시스템** 🔔
   - 브라우저 알림 지원
   - 매칭 요청/수락 알림
   - 새 메시지 알림
   - 읽음/안읽음 표시

6. **즐겨찾기/북마크** ⭐
   - 이벤트 북마크
   - 모임방 북마크
   - 즐겨찾기 목록 페이지
   - 빠른 접근 기능

7. **커뮤니티 통계** 📊
   - 전체 사용자/이벤트/모임방 통계
   - 인기 관심사 TOP 10
   - 연령대 분포
   - 인기 이벤트/모임방 TOP 5

8. **개선된 1:1 매칭** 💕
   - 매칭 수락 시 알림 전송
   - 수락된 매칭에 "메시지 보내기" 버튼
   - DM 스레드 자동 생성
   - 상태 표시 (대기/수락/거절)

### 관리자 페이지 (admin_page.html)
- ✅ 사용자 관리
- ✅ 모임방 관리
- ✅ 이벤트 관리
- ✅ 공지사항 관리 (생성/삭제)
- ✅ 게시판 관리 (삭제)
- ✅ 통계 대시보드

## 🔧 Firebase 설정

### 1. Security Rules 설정

Firebase Console > Firestore Database > 규칙(Rules)에 다음 코드를 붙여넣으세요:

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {

    // 사용자 프로필
    match /users/{userId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null && request.auth.uid == resource.data.userId;
      allow delete: if request.auth != null && request.auth.uid == resource.data.userId;
    }

    // 채팅방
    match /chatRooms/{roomId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null;
      allow delete: if request.auth != null && request.auth.uid == resource.data.creatorId;

      match /messages/{messageId} {
        allow read, write: if request.auth != null;
      }
    }

    // 이벤트
    match /events/{eventId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null;
      allow delete: if request.auth != null && request.auth.uid == resource.data.creatorId;
    }

    // 매칭 요청 (통계용 전체 읽기 허용)
    match /matchRequests/{requestId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null &&
                      (request.auth.uid == resource.data.senderId ||
                       request.auth.uid == resource.data.receiverId);
      allow delete: if request.auth != null && request.auth.uid == resource.data.senderId;
    }

    // Direct Messages
    match /directMessages/{threadId} {
      allow read: if request.auth != null &&
                     request.auth.uid in resource.data.participantIds;
      allow create: if request.auth != null;
      allow update: if request.auth != null &&
                      request.auth.uid in resource.data.participantIds;
      allow delete: if request.auth != null &&
                      request.auth.uid in resource.data.participantIds;

      match /messages/{messageId} {
        allow read, write: if request.auth != null;
      }
    }

    // 알림
    match /notifications/{notificationId} {
      allow read: if request.auth != null && request.auth.uid == resource.data.userId;
      allow create: if request.auth != null;
      allow update: if request.auth != null && request.auth.uid == resource.data.userId;
      allow delete: if request.auth != null && request.auth.uid == resource.data.userId;
    }

    // 북마크
    match /bookmarks/{bookmarkId} {
      allow read: if request.auth != null && request.auth.uid == resource.data.userId;
      allow create: if request.auth != null;
      allow update: if request.auth != null && request.auth.uid == resource.data.userId;
      allow delete: if request.auth != null && request.auth.uid == resource.data.userId;
    }

    // 공지사항
    match /notices/{noticeId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null;
      allow delete: if request.auth != null;
    }

    // 게시글
    match /posts/{postId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null;
      allow delete: if request.auth != null && request.auth.uid == resource.data.authorId;

      match /comments/{commentId} {
        allow read, write: if request.auth != null;
      }
    }
  }
}
```

### 2. 필수 인덱스 생성

다음 인덱스들을 생성하세요:

1. **directMessages 인덱스**
   - 컬렉션: `directMessages`
   - 필드: `participantIds` (배열), `lastMessageAt` (내림차순)

2. **notifications 인덱스**
   - 컬렉션: `notifications`
   - 필드: `userId` (오름차순), `createdAt` (내림차순)

3. **matchRequests 인덱스 (receiverId)**
   - 컬렉션: `matchRequests`
   - 필드: `receiverId` (오름차순), `createdAt` (내림차순)

4. **matchRequests 인덱스 (senderId)**
   - 컬렉션: `matchRequests`
   - 필드: `senderId` (오름차순), `createdAt` (내림차순)

> **팁**: 에러 메시지에 나오는 링크를 클릭하면 자동으로 인덱스 생성 페이지로 이동합니다!

## 📱 관심사 카테고리 (40개+)

- 🧘‍♀️ **건강·운동** (8개): 러닝, 헬스, 요가, 등산, 자전거, 수영, 걷기, 다이어트
- 🎨 **문화·취미** (10개): 영화, 음악, 독서, 미술, 공예, 캘리그라피, 사진, 요리, 커피, 보드게임
- 💻 **자기계발·디지털** (5개): 영어, 코딩, 재테크, 리더십, 스터디
- 👨‍👩‍👧 **가족·생활** (5개): 육아, 반려동물, 가드닝, 인테리어, 캠핑
- 💬 **사회·교류** (7개): 미혼모임, 돌싱모임, 시니어모임, 봉사, 플리마켓, 지역축제, 다문화
- 🌿 **힐링·정서** (3개): 명상, 힐링, 건강챌린지
- 🎮 **여가·테크취미** (4개): 게임, 드론, 레고, 유튜브

## 🛠️ 기술 스택

- React 18
- Firebase (Authentication + Firestore)
- Tailwind CSS
- CDN 기반 (별도 빌드 불필요)

## 🎉 주요 업데이트 히스토리

### 2025-11-21 - 메이저 업데이트 v3.0
- ✨ 공지사항 시스템 추가 (긴급/일반 구분)
- ✨ 모임 후기 게시판 추가 (댓글, 좋아요 기능)
- ✨ 전체 검색 및 필터링 기능 강화
- ✨ 무한스크롤/페이지네이션 구현
- ✨ 관리자 페이지에 공지/게시판 관리 기능 추가
- ✨ 대시보드 메뉴 확장 (7개 → 9개)
- 🐛 Firebase 보안 규칙 업데이트

### 2025-11-17 - 메이저 업데이트 v2.0
- ✨ 1:1 인앱 메시지 기능 추가
- ✨ 브라우저 알림 시스템 구현
- ✨ 즐겨찾기/북마크 기능 추가
- ✨ 커뮤니티 통계 대시보드 추가
- ✨ 대시보드 메뉴 확장 (3개 → 7개)
- ✨ 매칭 시스템 개선 (DM 자동 연동)
- ✨ 이벤트/모임방에 북마크 버튼 추가
- 🐛 Firebase 인덱스 및 보안 규칙 최적화

### 이전 업데이트
- ✅ 이벤트 CRUD 완성
- ✅ 모임방 CRUD 완성
- ✅ 카드 클릭으로 채팅방 입장
- ✅ 생성자 전용 수정/삭제 버튼

## 🔐 관리자 계정

관리자 계정은 Firebase Console에서 직접 설정하세요.
비밀번호는 코드나 문서에 기록하지 마세요.

## 📞 문의

Firebase 프로젝트: `woori-apartment`

---

**평택고덕행복주택 커뮤니티 앱 v3.0** 🏠
