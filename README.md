# 설문조사 사이트

- **관리자 페이지** (설문 만들기 / 결과 보기): https://dajoeunaofficial-sys.github.io/
- 설문을 만들면 공유 링크가 자동 복사됩니다. 응답자는 로그인 없이 답할 수 있어요.
- 데이터는 파이어베이스(dajoeuna-survey 프로젝트) Firestore에 저장됩니다.

## 남은 파이어베이스 설정

파이어베이스 콘솔(https://console.firebase.google.com, dajoeuna.official 계정)에서:

### 1. 보안 규칙 (아직 안 했다면)

Firestore Database → 규칙 탭 → 아래 내용으로 교체 → 게시

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /surveys/{surveyId} {
      allow read: if true;
      allow create: if request.auth != null
                    && request.resource.data.ownerUid == request.auth.uid;
      allow update, delete: if request.auth != null
                            && resource.data.ownerUid == request.auth.uid;
      match /responses/{responseId} {
        allow create: if true;
        allow read, delete: if request.auth != null
          && get(/databases/$(database)/documents/surveys/$(surveyId)).data.ownerUid == request.auth.uid;
      }
    }
  }
}
```

### 2. 구글 로그인 켜기 (아직 안 했다면)

Authentication → 시작하기 → Google → 사용 설정 → 지원 이메일 선택 → 저장

### 3. 승인된 도메인 추가

Authentication → 설정 탭 → 승인된 도메인 → 도메인 추가:

```
dajoeunaofficial-sys.github.io
```

## 문제가 생기면

| 증상 | 확인할 것 |
|------|-----------|
| 로그인 팝업이 안 뜨거나 에러 | 2번(구글 로그인), 3번(승인된 도메인) 확인 |
| "불러오기 실패" / "권한 없음" | 1번 보안 규칙을 게시했는지 확인 |
| 사이트가 404 | 저장소 이름이 정확히 `dajoeunaofficial-sys.github.io`인지, Public인지 확인 |
