# 마중물 장학회 메인앱 v2.0 — 배포 가이드

## 파일 구조
```
/
├── index.html          ← 메인앱 (Single Page App)
├── manifest.json       ← PWA 설정
├── firestore.rules     ← Firestore 보안 규칙
└── icons/
    ├── icon-192.png    ← PWA 아이콘 (직접 제작 필요)
    └── icon-512.png
```

---

## 1. Firebase 설정 교체 (필수)

`index.html` 안의 `firebaseConfig` 부분을 교체하세요.

Firebase Console → 프로젝트 설정 → 일반 → 내 앱 → SDK 설정 및 구성

```js
const firebaseConfig = {
  apiKey: "실제_API_KEY",
  authDomain: "mjm-join.firebaseapp.com",
  projectId: "mjm-join",
  storageBucket: "mjm-join.appspot.com",
  messagingSenderId: "실제_SENDER_ID",
  appId: "실제_APP_ID"
};
```

---

## 2. Firebase Console 설정

### Authentication
- 이메일/비밀번호 로그인 활성화
- Google 로그인 활성화
- 승인된 도메인에 `nambongwoo.github.io` 추가

### Firestore
- 데이터베이스 생성 (asia-northeast3 / 서울 권장)
- 보안 규칙: `firestore.rules` 내용 붙여넣기

### Firestore 초기 데이터
관리자 계정 생성 후 Firestore에서 직접 `users/{uid}` 문서에 아래 필드 추가:
```json
{
  "role": "admin",
  "approved": true
}
```

---

## 3. GitHub Pages 배포

```bash
# 레포지토리에 파일 업로드
git add index.html manifest.json firestore.rules icons/
git commit -m "feat: v2.0 main app"
git push origin main

# GitHub 레포 설정 → Pages → Source: main branch / root
```

---

## 4. Firestore 컬렉션 구조

### users
| 필드 | 타입 | 설명 |
|------|------|------|
| uid | string | Firebase Auth UID |
| email | string | 이메일 |
| name | string | 이름 |
| role | string | `admin` / `manager` / `member` / `pending` |
| approved | boolean | 관리자 승인 여부 |
| phone | string | 연락처 |
| bio | string | 자기소개 |
| createdAt | timestamp | 가입일 |

### notices
| 필드 | 타입 | 설명 |
|------|------|------|
| title | string | 제목 |
| content | string | 내용 |
| type | string | `공지` / `일반` / `중요` |
| authorUid | string | 작성자 UID |
| authorName | string | 작성자 이름 |
| createdAt | timestamp | 작성일 |

### events
| 필드 | 타입 | 설명 |
|------|------|------|
| title | string | 일정명 |
| date | timestamp | 날짜 |
| place | string | 장소 |
| desc | string | 설명 |
| createdAt | timestamp | 등록일 |

---

## 5. 권한 흐름

```
신규 가입 → role: pending, approved: false
         ↓ (관리자 패널 → 가입 승인 클릭)
         → role: member, approved: true
         ↓ (관리자가 수동으로 역할 변경)
         → role: manager 또는 admin
```

---

## 6. 추후 개발 예정

- [ ] FCM 푸시 알림
- [ ] 장학금 신청/지급 관리 모듈
- [ ] 납부 현황 (회비/기부금)
- [ ] 파일/자료실
- [ ] 회원 가입 신청 폼 연동 (v1.0 데이터 마이그레이션)
