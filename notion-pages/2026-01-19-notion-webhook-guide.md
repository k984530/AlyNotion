# 2026.01.19 Notion Webhook 완벽 가이드 - 실시간 자동화를 위한 웹훅 활용법

## 개요

Notion Webhook은 워크스페이스의 변경 사항을 실시간으로 외부 시스템에 전달하는 기능입니다. 이 문서는 개발자용 Integration Webhook과 노코드 Webhook Action 두 가지 방식을 모두 다루며, 설정부터 실전 활용까지 상세히 안내합니다.

---

## 목차

1. Webhook의 두 가지 유형
2. Integration Webhook (개발자 API)
3. Webhook Action (노코드)
4. 이벤트 타입과 페이로드
5. 보안 및 서명 검증
6. 실전 활용 사례
7. 외부 도구 연동 (Make, Zapier, n8n)
8. 제한사항 및 주의점
9. 참고 자료

---

## 1. Webhook의 두 가지 유형

Notion에서 웹훅을 사용하는 방법은 크게 두 가지입니다:

### Integration Webhook (개발자 API)

- Notion API를 통해 구독 생성
- 워크스페이스 전체의 변경 사항 수신 가능
- 페이지 생성/수정/삭제, 데이터베이스 스키마 변경 등 감지
- 서명 검증을 통한 보안 지원
- 개발자용, 코드 작성 필요

### Webhook Action (노코드)

- Notion UI에서 직접 설정
- 버튼, 데이터베이스 버튼, 자동화에서 사용
- HTTP POST 요청을 지정된 URL로 전송
- 유료 플랜에서 사용 가능
- 노코드/로우코드 플랫폼과 연동에 적합

---

## 2. Integration Webhook (개발자 API)

### 2.1 개념

Integration Webhook을 사용하면 Notion 워크스페이스에서 발생하는 변경 사항을 실시간으로 수신할 수 있습니다. 기존의 폴링(polling) 방식과 달리, Notion이 변경 사항을 직접 알려주므로 효율적입니다.

### 2.2 설정 방법

**1단계: Integration 생성**

```
1. https://www.notion.so/my-integrations 접속
2. + New integration 클릭
3. Integration 이름 및 워크스페이스 선택
4. Capabilities에서 필요한 권한 설정
5. Submit
```

**2단계: Webhook 구독 생성**

```
1. Integration 설정 → Webhooks 탭
2. + Create a subscription 클릭
3. 공개 Webhook URL 입력 (SSL 필수, localhost 불가)
4. 구독할 이벤트 타입 선택
5. Create subscription
```

**3단계: 검증 토큰 저장**

구독 생성 시 Notion이 검증 요청을 보냅니다:

```json
{
  "verification_token": "secret_xxxxx...your_verification_token"
}
```

이 토큰을 안전하게 저장하고, Integration UI에서 검증을 완료합니다.

### 2.3 페이지 접근 권한 설정

Integration이 페이지 변경을 감지하려면 해당 페이지에 대한 접근 권한이 필요합니다:

```
1. 모니터링할 Notion 페이지 열기
2. 우측 상단 ··· → Connections
3. 생성한 Integration 선택하여 연결
```

---

## 3. Webhook Action (노코드)

### 3.1 개념

Webhook Action은 코드 없이 Notion UI에서 직접 설정하는 웹훅입니다. 버튼 클릭이나 데이터베이스 자동화 트리거 시 HTTP POST 요청을 전송합니다.

### 3.2 사용 위치

- **페이지 버튼**: 정적 커스텀 헤더만 전송 가능
- **데이터베이스 버튼**: 페이지 속성 데이터 자동 포함
- **데이터베이스 자동화**: 조건 충족 시 자동 실행

### 3.3 데이터베이스 버튼 설정

```
1. 데이터베이스 열기
2. 속성 추가 → Button 타입 선택
3. 버튼 클릭 시 동작 → Send webhook 선택
4. Webhook URL 입력 (Make, Zapier 등에서 제공)
5. 전송할 속성 선택 (선택사항)
6. 저장
```

### 3.4 데이터베이스 자동화 설정

```
1. 데이터베이스 우측 상단 ⚡ 아이콘 클릭
2. + New automation
3. 트리거 조건 설정 (예: 상태가 "완료"로 변경될 때)
4. 액션 추가 → Send webhook
5. URL 및 전송할 속성 설정
6. 활성화
```

---

## 4. 이벤트 타입과 페이로드

### 4.1 지원 이벤트 타입 (API 2025-09-03 버전)

**페이지 이벤트**

| 이벤트 | 설명 | 집계 여부 |
|--------|------|-----------|
| `page.created` | 페이지 생성 | X |
| `page.deleted` | 페이지 삭제 | X |
| `page.undeleted` | 페이지 복원 | X |
| `page.content_updated` | 페이지 내용 변경 | O (최대 1분 지연) |
| `page.locked` | 페이지 잠금 | X |

**데이터베이스 이벤트**

| 이벤트 | 설명 |
|--------|------|
| `data_source.schema_updated` | 데이터베이스 스키마 변경 (2025-09-03) |
| `database.schema_updated` | 데이터베이스 스키마 변경 (레거시) |

**코멘트 이벤트**

| 이벤트 | 설명 |
|--------|------|
| `comment.created` | 코멘트 생성 (comment read 권한 필요) |

### 4.2 이벤트 집계 (Aggregation)

`page.content_updated`와 같이 빈번하게 발생하는 이벤트는 짧은 시간 내 변경 사항을 하나로 묶어 전송합니다:

- 중복 알림 감소
- 최대 1분 정도 지연 발생 가능
- 연속된 생성→삭제→복원 시 최종 상태만 전송될 수 있음

### 4.3 페이로드 구조 예시

**검증 요청**

```json
{
  "verification_token": "secret_xxx..."
}
```

**이벤트 페이로드**

```json
{
  "id": "evt_xxx",
  "type": "page.content_updated",
  "timestamp": "2026-01-19T10:30:00.000Z",
  "workspace_id": "ws_xxx",
  "entity": {
    "type": "page",
    "id": "page_xxx"
  },
  "authors": [
    {"id": "user_xxx", "type": "person"}
  ],
  "data": {
    "parent": {
      "type": "database_id",
      "id": "db_xxx"
    }
  }
}
```

### 4.4 이벤트 전달 특성

- 대부분 1분 이내 전달, 최대 5분
- at-most-once 전달 보장
- 실패 시 최대 8회 재시도 (지수 백오프)
- 최종 재시도는 최초 발생 후 약 24시간

---

## 5. 보안 및 서명 검증

### 5.1 서명 검증의 중요성

프로덕션 환경에서는 반드시 서명을 검증하여 요청이 Notion에서 온 것인지 확인해야 합니다. 서명이 없으면 악의적인 요청을 걸러낼 수 없습니다.

### 5.2 X-Notion-Signature 헤더

모든 웹훅 요청에는 `X-Notion-Signature` 헤더가 포함됩니다:

```
X-Notion-Signature: sha256=461e8cbcba8a75c3edd866f0e71280f5a85cbf21eff040ebd10fe266df38a735
```

### 5.3 서명 검증 방법

**Node.js 예시**

```javascript
const crypto = require('crypto');

function verifyNotionWebhook(req, verificationToken) {
  const signature = req.headers['x-notion-signature'];
  const body = JSON.stringify(req.body);

  // HMAC-SHA256으로 서명 생성
  const expectedSignature = 'sha256=' + crypto
    .createHmac('sha256', verificationToken)
    .update(body)
    .digest('hex');

  // 타이밍 안전 비교
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```

**Python 예시**

```python
import hmac
import hashlib

def verify_notion_webhook(request_body, signature_header, verification_token):
    expected_signature = 'sha256=' + hmac.new(
        verification_token.encode(),
        request_body.encode(),
        hashlib.sha256
    ).hexdigest()

    return hmac.compare_digest(signature_header, expected_signature)
```

### 5.4 보안 베스트 프랙티스

- 검증 토큰을 환경 변수로 관리
- 90일마다 토큰 로테이션 권장
- 검증 실패 로깅 (토큰 자체는 로깅 금지)
- 웹훅 엔드포인트에 Rate Limiting 적용
- 항상 HTTPS 사용

---

## 6. 실전 활용 사례

### 6.1 작업 완료 시 Slack 알림

**시나리오**: 데이터베이스의 상태가 "완료"로 변경되면 Slack 채널에 알림

**구현 (n8n)**

```
Notion Webhook Trigger → IF (status == "완료") → Slack Node
```

### 6.2 폼 제출 시 데이터베이스 자동 기록

**시나리오**: 외부 폼 제출 데이터를 Notion 데이터베이스에 자동 추가

**구현 (Make)**

```
Webhook → Parse JSON → Notion: Create Database Item
```

### 6.3 소셜 미디어 자동 게시

**시나리오**: Notion에서 게시물 작성 후 버튼 클릭으로 소셜 미디어 게시

**구현**

```
1. Notion 데이터베이스에 "게시" 버튼 속성 추가
2. 버튼 → Send webhook → Buffer/Hootsuite API
3. 게시물 내용과 이미지 URL을 속성으로 전달
```

### 6.4 CRM 동기화

**시나리오**: Notion 고객 데이터베이스 변경 시 CRM 자동 업데이트

**구현**

```
Notion Integration Webhook →
Filter (page.content_updated, 고객 DB) →
Notion API로 상세 조회 →
CRM API로 업데이트
```

### 6.5 GitHub Issue 자동 생성

**시나리오**: Notion에서 버그 리포트 생성 시 GitHub Issue 자동 생성

**구현**

```
DB Automation (새 페이지 생성 시) →
Send Webhook → n8n/Zapier →
GitHub: Create Issue
```

---

## 7. 외부 도구 연동

### 7.1 Make (Integromat)

**Webhook URL 생성**

```
1. Make에서 새 시나리오 생성
2. Webhooks → Custom webhook 모듈 추가
3. Add → 새 웹훅 생성
4. 생성된 URL을 Notion에 등록
```

**장점**

- 시각적 워크플로우 빌더
- 다양한 앱 통합 지원
- 무료 플랜 제공 (제한적)

### 7.2 Zapier

**Webhook URL 생성**

```
1. Zapier에서 새 Zap 생성
2. Trigger: Webhooks by Zapier → Catch Hook
3. 생성된 URL을 Notion에 등록
4. 테스트 요청 전송 후 데이터 구조 확인
```

**장점**

- 사용하기 쉬운 인터페이스
- 5,000+ 앱 연동
- AI 기반 Zap 생성 지원

### 7.3 n8n

**Webhook URL 생성**

```
1. n8n에서 새 워크플로우 생성
2. Webhook 노드 추가
3. HTTP Method: POST 선택
4. 생성된 URL을 Notion에 등록
```

**장점**

- 완전 무료 (셀프 호스팅)
- 오픈소스
- 무제한 실행
- 14개 Notion 액션 + 2개 트리거 지원

### 7.4 Pipedream

**Webhook URL 생성**

```
1. Pipedream에서 새 워크플로우 생성
2. Trigger: HTTP / Webhook 선택
3. 생성된 URL을 Notion에 등록
```

**장점**

- 개발자 친화적
- 코드 작성 가능
- 관대한 무료 플랜

---

## 8. 제한사항 및 주의점

### 8.1 Integration Webhook 제한

| 항목 | 제한 |
|------|------|
| 엔드포인트 | SSL(HTTPS) 필수, localhost 불가 |
| URL 변경 | 불가 (삭제 후 재생성 필요) |
| 이벤트 전달 | at-most-once (중복 방지) |
| 재시도 | 최대 8회, 24시간 이내 |

### 8.2 Webhook Action 제한

| 항목 | 제한 |
|------|------|
| 자동화당 웹훅 | 최대 5개 |
| HTTP 메소드 | POST만 지원 |
| 전송 데이터 | 페이지 속성만 (내용 불가) |
| 플랜 요구사항 | 유료 플랜 필요 |
| 워크스페이스 레벨 | 미지원 (개발 중) |

### 8.3 일반적인 문제 해결

**웹훅이 동작하지 않을 때**

1. Integration이 해당 페이지에 연결되어 있는지 확인
2. 필요한 Capabilities가 활성화되어 있는지 확인
3. 구독 상태가 active인지 확인
4. 이벤트 집계로 인한 지연 고려 (최대 1분)

**페이로드 테스트**

Notion은 페이로드 미리보기를 제공하지 않습니다. webhook.site 등을 사용하여 테스트:

```
1. https://webhook.site 접속
2. 제공된 URL을 Notion에 등록
3. 버튼 클릭 또는 자동화 트리거
4. 수신된 페이로드 확인
```

### 8.4 오류 처리

Webhook Action 실패 시:

- 자동화 옆에 느낌표(!) 표시
- 자동화가 자동으로 일시 중지
- 수동으로 재개 필요

---

## 9. 참고 자료

### 공식 문서

- Notion Webhooks API: https://developers.notion.com/reference/webhooks
- Event Types & Delivery: https://developers.notion.com/reference/webhooks-events-delivery
- Webhook Actions Help: https://www.notion.com/help/webhook-actions
- Database Automations: https://www.notion.com/help/database-automations

### 가이드 및 튜토리얼

- Notion Webhooks Complete Guide (2025): https://dev.to/robbiecahill/notion-webhooks-a-complete-guide-for-developers-2025-hop
- How to trigger webhooks from Notion: https://www.simonesmerilli.com/life/webhook-notion
- Social Media Posts with Webhook Actions: https://www.notion.com/help/guides/share-social-media-posts-from-notion-with-webhook-actions

### 연동 플랫폼

- Make (Integromat): https://www.make.com
- Zapier: https://zapier.com
- n8n: https://n8n.io
- Pipedream: https://pipedream.com

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-19
- 버전: 1.0
