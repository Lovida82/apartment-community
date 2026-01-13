# 평택고덕행복주택 커뮤니티 앱

## 프로젝트 개요
- **프로젝트명**: 평택고덕행복주택 커뮤니티
- **기술 스택**: React 18 (CDN), Firebase (Auth + Firestore), Tailwind CSS
- **배포**: Netlify (https://happyhousecomm.ideastudio.one/)
- **GitHub**: https://github.com/Lovida82/apartment-community

## 파일 구조
```
Apartment_firebase_251121/
├── index.html          # 메인 앱 (사용자용)
├── admin_page.html     # 관리자 페이지
├── CLAUDE.md           # 프로젝트 문서 (이 파일)
├── .gitignore          # Git 제외 파일 목록
├── dist/               # 배포용 폴더 (Netlify 배포 대상)
│   ├── index.html
│   ├── admin_page.html
│   ├── CLAUDE.md
│   └── README.md
└── .netlify/           # Netlify 로컬 설정 (Git 제외)
```

## Firebase 설정
```javascript
const firebaseConfig = {
    apiKey: "AIzaSyDz84lvk87Uu6uZJM0jBXA47-HHCtQ0dO8",
    authDomain: "woori-apartment.firebaseapp.com",
    projectId: "woori-apartment",
    storageBucket: "woori-apartment.firebasestorage.app",
    messagingSenderId: "783406954743",
    appId: "1:783406954743:web:42c296d03ef9c1c1883bf2"
};
```

## 구현된 기능

### 1. 인증 시스템
- 이메일/비밀번호 회원가입 및 로그인
- 관리자 계정: Firebase Console에서 설정 (비밀번호 비공개)
- 프로필 정보: 닉네임, 카카오ID, 동(6201~6204), 연령대, 성별, 관심사

### 2. 메인 앱 (index.html)

#### 2.1 대시보드 (DashboardPage)
- 컴팩트 헤더 + 프로필 모달
- 날씨 위젯 (Open-Meteo API - 평택고덕 좌표)
- 인기글 섹션 (좋아요 기준 상위 3개)
- 4열 메뉴 그리드: 모임방, 이벤트, 매칭, 메시지, 공지, 캘린더, 즐겨찾기, 통계
- 게시판 바로가기: 모임후기, 맛집추천, 나눔·중고거래
- 검색 버튼 (헤더)

#### 2.2 게시판 시스템
- **BoardHubPage**: 게시판 허브
- **PostsPage**: 모임 후기 게시판
- **RecommendationsPage**: 맛집·업체 추천 게시판
- **SharingPage**: 나눔 게시판 (기존)
- **TradingPage**: 나눔·중고거래 통합 (카테고리: 무료나눔, 판매)

#### 2.3 소셜 기능
- **ChatRoomsPage**: 모임방 (관심사 기반)
- **EventsPage**: 이벤트 생성/참가/취소
- **MatchingPage**: 1:1 매칭 (관심사 기반)
- **DirectMessagesPage**: 쪽지함

#### 2.4 사용자 기능
- **MyPage**: 마이페이지 (프로필 수정, 즐겨찾기, 통계, 요청/문의)
- **SettingsPage**: 프로필 수정
- **BookmarksPage**: 즐겨찾기한 게시물
- **StatisticsPage**: 활동 통계
- **NotificationsPage**: 알림
- **NoticesPage**: 공지사항

#### 2.5 검색 및 캘린더
- **SearchPage**: 통합 검색 (게시글, 추천, 나눔, 채팅방, 이벤트)
- **CalendarPage**: 월별 이벤트 달력

#### 2.6 신고/차단/팔로우
- **UserProfilePage**: 다른 사용자 프로필 보기
  - 팔로우/언팔로우 기능
  - 차단/차단해제 기능
  - 신고 기능 (스팸, 욕설, 부적절, 사기, 괴롭힘)
  - 활동 배지 표시 (신규, 활발한 참여자, 인기인, 이벤트 마스터)

#### 2.7 피드백 시스템
- **FeedbackPage**: 요청/문의 (기능 제안, 오류 신고, 문의, 기타)

### 3. 관리자 페이지 (admin_page.html)
- **통계 탭**: 전체 사용자, 모임방, 이벤트 현황
- **사용자 탭**: 사용자 목록 및 삭제
- **모임방 탭**: 모임방 관리
- **이벤트 탭**: 이벤트 관리
- **공지사항 탭**: 공지 작성/삭제
- **모임후기 탭**: 게시글 관리
- **맛집추천 탭**: 추천글 관리
- **나눔 탭**: 나눔글 관리
- **피드백 탭**: 요청/문의 관리 (상태: 대기중→검토중→완료)
- **신고 탭**: 신고 관리 (상태: 대기중→검토중→처리완료/기각)

### 4. 하단 네비게이션 (BottomNav)
- 홈 (대시보드)
- 게시판 (게시판 허브)
- 알림 (알림 페이지)
- MY (마이페이지)

## Firestore 컬렉션

| 컬렉션 | 용도 |
|--------|------|
| users | 사용자 프로필 (followers, following, blockedUsers 배열 포함) |
| chatRooms | 모임방 |
| chatRooms/{id}/messages | 채팅 메시지 |
| events | 이벤트 |
| posts | 모임후기 게시글 |
| posts/{id}/comments | 댓글 |
| recommendations | 맛집·업체 추천 |
| recommendations/{id}/comments | 댓글 |
| sharing | 나눔 게시글 |
| sharing/{id}/comments | 댓글 |
| trading | 중고거래 게시글 (나눔 확장) |
| trading/{id}/comments | 댓글 |
| matchRequests | 매칭 요청 |
| directMessages | 쪽지 스레드 |
| directMessages/{id}/messages | 쪽지 메시지 |
| notifications | 알림 |
| bookmarks | 즐겨찾기 |
| notices | 공지사항 |
| feedback | 피드백/요청사항 |
| reports | 신고 |

## FirebaseUtils 주요 함수

```javascript
// 사용자 관련
getUserProfile(userId)
createUserProfile(userId, profileData)
updateUserProfile(userId, updates)
getUserProfileById(targetUserId)

// 인기글 & 검색
getPopularPosts(limitCount = 5)
searchAll(query)

// 신고/차단/팔로우
reportContent(reportData)
blockUser(userId, blockedUserId)
unblockUser(userId, blockedUserId)
followUser(userId, targetUserId)
unfollowUser(userId, targetUserId)

// 배지 계산
calculateBadges(userProfile, stats)
```

## 아이콘 컴포넌트
모든 아이콘은 SVG 기반 React 컴포넌트로 구현:
- Users, MessageCircle, Calendar, Heart, Plus, Send, X, ChevronLeft, ChevronRight
- Edit, Trash, Settings, LogOut, Home, Bell, Bookmark, BarChart, HelpCircle
- MapPin, Gift, FileText, Mail, Search, AlertCircle, Star, Check, Clock
- ThumbsUp, Eye, Menu, User, Filter, TrendingUp, Flag, UserX, UserPlus
- Award, Cloud, DollarSign

## 스타일링
- Tailwind CSS (CDN)
- 커스텀 CSS: loading 애니메이션, safe-area-bottom, slide-up 애니메이션
- 반응형 디자인 (모바일 최적화)

## 주요 변경 이력

### 2026-01-13
- **GitHub 리포지토리 생성** (Lovida82/apartment-community)
- **Netlify-GitHub 자동 배포 연동 완료**
  - master 브랜치 푸시 시 자동 배포
  - Base directory: (비움), Publish directory: `dist`
- 자동 배포 테스트 완료

### 2026-01-12
- 인기글 섹션 + 날씨 위젯 추가
- 통합 검색 페이지 추가
- 캘린더 페이지 추가
- 신고/차단 기능 추가
- 프로필 방문 페이지 추가
- 팔로우 시스템 + 배지 추가
- 나눔→중고거래 확장 (TradingPage)
- 관리자 페이지 신고 관리 탭 추가
- Firebase 초기화 이벤트 방식 개선 (firebaseReady 이벤트)

## 배포 방법 (GitHub 자동 배포)
1. 코드 수정 후 dist 폴더에 복사:
   ```bash
   cp index.html dist/index.html
   cp admin_page.html dist/admin_page.html
   ```
2. Git 커밋 및 푸시:
   ```bash
   git add .
   git commit -m "변경 내용 설명"
   git push
   ```
3. Netlify에서 자동 배포 (master 브랜치 → dist 폴더)
4. 배포 확인: https://app.netlify.com/projects/happyhousecomm/deploys

## 배포 시 주의사항
- Firebase 콘솔에서 Firestore 보안 규칙 업데이트 필요
- Netlify 빌드 설정: Base directory = (비움), Publish directory = `dist`
- 크레딧: 무료 플랜 월 800 크레딧, 정적 배포는 크레딧 거의 소모 안 함

## Firestore 보안 규칙
별도 파일 또는 Firebase 콘솔에서 관리
- 기본 인증 필수 (request.auth != null)
- 본인 데이터만 수정/삭제 가능
- 관리자(admin@admin.com) 특별 권한
