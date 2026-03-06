# 2026.03.06 AlyBrowser - AI 에이전트용 브라우저 자동화 SDK

## 📋 목차

1. 개요
2. 핵심 아키텍처
3. 프로젝트 구조
4. 설치 및 설정
5. 주요 기능
6. 멀티 세션 지원 (v0.4.0)
7. MCP 도구 목록
8. 버전 히스토리
9. 향후 계획

---

## 1. 개요

AlyBrowser는 AI 에이전트(Claude Code 등)가 실제 Chrome 브라우저를 제어할 수 있게 해주는 경량 브라우저 자동화 SDK입니다.

기존 브라우저 자동화 도구(Puppeteer, Playwright 등)와 달리 **Chrome Extension Bridge** 방식을 사용하여 CDP(Chrome DevTools Protocol)를 우회합니다. 이로 인해 봇 탐지 시스템을 자연스럽게 회피하며, 실제 사용자와 동일한 브라우저 환경에서 동작합니다.

### 핵심 특징

- **봇 탐지 우회** — CDP 미사용, Extension Bridge 통신으로 자연스러운 브라우저 동작
- **접근성 트리 스냅샷** — @eN ref ID 시스템으로 페이지 요소를 효율적으로 식별
- **MCP 프로토콜** — Model Context Protocol 기반으로 AI 에이전트와 직접 통합
- **멀티 세션** — 독립된 Chrome 인스턴스로 멀티 계정 동시 운영 가능 (v0.4.0)
- **Site Knowledge** — 사이트별 성공/실패 경험을 기록하여 세션 간 학습 유지
- **최소 의존성** — 런타임 의존성 2개 (@modelcontextprotocol/sdk, ws)

---

## 2. 핵심 아키텍처

AlyBrowser는 Extension Bridge 아키텍처를 사용합니다. Node.js MCP 서버가 WebSocket으로 Chrome Extension과 통신하고, Extension이 Content Script를 통해 실제 페이지를 제어합니다.

### 통신 흐름

```
AI Agent (Claude Code)
    │
    ▼
MCP Server (Node.js)          ← src/mcp/server.ts
    │
    ▼ WebSocket (port 19222~19322)
    │
Chrome Extension              ← extension/background.js
    │  (background service worker)
    │
    ▼ chrome.tabs.sendMessage
    │
Content Script                ← extension/content.js
    │  (접근성 트리 생성, DOM 조작)
    │
    ▼
실제 웹 페이지
```

### 왜 CDP가 아닌 Extension Bridge인가?

CDP(Chrome DevTools Protocol)는 브라우저 자동화의 표준이지만, 많은 웹사이트가 CDP 연결을 탐지합니다:

- `navigator.webdriver` 플래그가 true로 설정됨
- Chrome DevTools 디버거 프로토콜 연결 흔적
- 자동화 관련 Chrome 플래그 (--remote-debugging-port 등)

Extension Bridge는 일반 Chrome Extension과 동일한 방식으로 동작하므로, 봇 탐지 시스템에 노출되지 않습니다. 실제 사용자가 Extension을 설치한 것과 구분할 수 없습니다.

### @eN Ref ID 시스템

Content Script가 페이지의 접근성 트리를 스캔하여 상호작용 가능한 요소에 @eN 형식의 ref ID를 부여합니다. 이 ID는 에페메럴(ephemeral)하며, 매 snapshot() 호출마다 리셋됩니다.

```
# snapshot 결과 예시
[page] Google
  [search] @e1 "검색"
  [button] @e2 "Google 검색"
  [button] @e3 "I am Feeling Lucky"
  [link]   @e4 "Gmail"
  [link]   @e5 "이미지"
```

---

## 3. 프로젝트 구조

```
AlyBrowser/
├── extension/                    # Chrome Extension
│   ├── manifest.json             # Extension 매니페스트 (Manifest V3)
│   ├── background.js             # Service Worker (WS 클라이언트)
│   └── content.js                # Content Script (접근성 트리, DOM 조작)
├── src/
│   ├── extension/
│   │   └── bridge.ts             # ★ 핵심: WS 서버 + Chrome 런처
│   ├── mcp/
│   │   ├── server.ts             # MCP 서버 (도구 핸들러)
│   │   ├── tools.ts              # 도구 정의 (30+ tools)
│   │   ├── site-knowledge.ts     # Site Knowledge 엔진
│   │   └── index.ts              # MCP 엔트리포인트
│   ├── chrome/
│   │   └── finder.ts             # Chrome 실행 파일 탐색
│   ├── utils/
│   │   ├── deferred.ts           # Promise 유틸
│   │   └── logger.ts             # 로깅
│   ├── errors.ts                 # 에러 클래스
│   └── index.ts                  # 라이브러리 엔트리포인트
├── ~/.aly-browser/               # 런타임 데이터 (홈 디렉토리)
│   └── sessions/
│       ├── default/              # 기본 세션
│       │   ├── profile/          # Chrome 프로필 (쿠키, 설정 등)
│       │   ├── extension/        # 포트 주입된 Extension 복사본
│       │   ├── port              # WS 서버 포트 번호
│       │   └── pid               # 프로세스 ID
│       ├── insta-a/              # 멀티 세션 예시
│       └── insta-b/
└── package.json
```

### 핵심 파일 설명

- `src/extension/bridge.ts` — 프로젝트의 핵심. WS 서버를 띄우고, Chrome을 실행하며, Extension 연결을 관리합니다. 세션별 격리된 프로필과 Extension 복사를 처리합니다.
- `src/mcp/server.ts` — MCP 프로토콜 서버. AI 에이전트의 도구 호출을 받아 bridge로 전달합니다. 멀티 세션 라우팅, Site Knowledge 통합을 담당합니다.
- `extension/background.js` — Chrome Extension의 Service Worker. WS 클라이언트로 동작하며, MCP 서버의 명령을 Content Script로 전달합니다.
- `extension/content.js` — 실제 웹 페이지에 주입되는 Content Script. 접근성 트리를 생성하고, 클릭/타이핑/스크롤 등의 DOM 조작을 수행합니다.

---

## 4. 설치 및 설정

### npm 패키지 설치

```bash
npm install aly-browser
```

### Claude Code MCP 설정

프로젝트 루트의 .mcp.json 파일에 다음을 추가합니다:

```json
{
  "mcpServers": {
    "aly-browser": {
      "command": "npx",
      "args": ["aly-browser"]
    }
  }
}
```

### 의존성

- **런타임**: @modelcontextprotocol/sdk, ws (총 2개)
- **빌드**: tsup (CJS + ESM 듀얼 빌드), typescript, vitest
- **요구사항**: Chrome 또는 Chromium 설치 필요

---

## 5. 주요 기능

### 5-1. 브라우저 제어

기본적인 브라우저 자동화 기능을 제공합니다. 모든 도구에 tabId 파라미터를 전달하여 멀티 탭 병렬 작업이 가능합니다.

- `browser_launch` / `browser_close` — Chrome 인스턴스 실행/종료
- `browser_navigate` / `browser_back` / `browser_forward` — 페이지 이동
- `browser_click` / `browser_type` / `browser_hover` — 요소 상호작용
- `browser_scroll` — 페이지 스크롤
- `browser_select` — <select> 요소 옵션 선택

### 5-2. 페이지 읽기

- `browser_snapshot` — 접근성 트리 스냅샷 캡처 (핵심 도구). @eN ref ID 포함
- `browser_html` — 페이지 HTML 소스 코드 반환
- `browser_eval` — 페이지 컨텍스트에서 JavaScript 실행

### 5-3. 대기 메커니즘

MutationObserver 기반의 지능형 대기 시스템:

- `browser_wait` — CSS 셀렉터가 나타나거나(hidden:false) 사라질 때까지(hidden:true) 대기
- `browser_wait_for_stable` — DOM 변화가 일정 시간(기본 500ms) 동안 없으면 로딩 완료로 판정. SPA 렌더링 완료 감지에 적합
- `browser_sleep` — 고정 1초 대기 (단순 딜레이용)

### 5-4. Site Knowledge (핵심 기능)

사이트별 성공/실패 경험을 기록하여 세션 간 학습을 유지하는 시스템입니다. 같은 실수를 반복하지 않도록 AI 에이전트에게 사이트 지식을 자동으로 제공합니다.

- `browser_learn` — 경험 기록 (URL, 액션, 성공/실패, 노트)
- `browser_get_knowledge` — 기록된 사이트 지식 조회

**자동 첨부**: navigate/launch 시 전체 포맷(최대 20개), snapshot 시 컴팩트 포맷(최대 5개)으로 관련 지식이 자동 첨부됩니다.

### 5-5. 탭 관리

- `browser_tab_list` / `browser_tab_new` / `browser_tab_close` / `browser_tab_switch`

**멀티 탭 병렬 작업 규칙**: 에이전트 수 = 탭 수 (1:1 매핑). 각 에이전트가 전용 tabId로 독립적으로 작업합니다.

### 5-6. 기타 Chrome API

- **쿠키**: browser_cookie_get / set / delete
- **스토리지**: browser_storage_get / set
- **북마크**: browser_bookmark_list / create / delete
- **알람**: browser_alarm_create / list / clear
- **기타**: browser_download, browser_history_search, browser_notify, browser_clipboard_read/write, browser_top_sites

---

## 6. 멀티 세션 지원 (v0.4.0)

v0.4.0에서 추가된 핵심 기능입니다. 각 세션이 완전히 격리된 Chrome 인스턴스를 가지므로, 같은 서비스에 여러 계정으로 동시 로그인이 가능합니다.

### 문제 배경

기존 싱글 세션 + 멀티 탭 구조에서는 모든 탭이 같은 Chrome 프로필(쿠키, localStorage)을 공유합니다. 이 때문에 Instagram 계정 A로 로그인한 상태에서 다른 탭에서 계정 B에 로그인하면, 계정 A가 로그아웃됩니다.

### 해결 방법: 세션별 격리

- **독립 프로필** — 세션마다 별도의 --user-data-dir (쿠키, 로그인 상태, 설정 등 완전 분리)
- **동적 포트** — 각 세션이 19222~19322 범위에서 동적으로 WS 포트 할당. bindWSServer()로 TOCTOU 레이스 방지
- **Extension 포트 주입** — Extension 디렉토리를 세션별로 복사한 뒤, background.js의 WS_PORT를 해당 세션 포트로 교체
- **세션 ID 검증** — /^[a-zA-Z0-9_-]+$/ 정규식으로 path traversal 공격 방지

### 사용 예시

```python
# 계정 A용 세션
browser_launch(sessionId="insta-a", url="https://instagram.com")

# 계정 B용 세션 (완전 독립)
browser_launch(sessionId="insta-b", url="https://instagram.com")

# 각 세션에서 독립적으로 로그인/작업
browser_click(ref="@e3", sessionId="insta-a")
browser_click(ref="@e3", sessionId="insta-b")

# 세션 관리
browser_session_list()      # 활성 세션 목록
browser_session_close_all() # 전체 세션 종료
```

### 하위 호환성

sessionId를 생략하면 자동으로 `"default"` 세션을 사용합니다. 기존 코드 변경 없이 동작합니다.

---

## 7. MCP 도구 전체 목록

총 32개의 MCP 도구를 제공합니다. 모든 도구에 sessionId 파라미터(선택)를 전달할 수 있습니다.

### 브라우저 제어 (5)

`browser_launch`, `browser_navigate`, `browser_back`, `browser_forward`, `browser_close`

### Site Knowledge (2)

`browser_learn`, `browser_get_knowledge`

### 페이지 읽기 (3)

`browser_snapshot`, `browser_html`, `browser_eval`

### 페이지 상호작용 (8)

`browser_click`, `browser_type`, `browser_select`, `browser_hover`, `browser_scroll`, `browser_wait`, `browser_wait_for_stable`, `browser_sleep`

### 탭 관리 (4)

`browser_tab_list`, `browser_tab_new`, `browser_tab_close`, `browser_tab_switch`

### 쿠키 (3) / 스토리지 (2) / 북마크 (3) / 알람 (3)

`browser_cookie_get/set/delete`, `browser_storage_get/set`, `browser_bookmark_list/create/delete`, `browser_alarm_create/list/clear`

### 기타 (4)

`browser_download`, `browser_history_search`, `browser_notify`, `browser_top_sites`, `browser_clipboard_read/write`

### 세션 관리 (2)

`browser_session_list`, `browser_session_close_all`

---

## 8. 버전 히스토리

- **v0.2.0** — Initial release. Extension Bridge 기반 브라우저 자동화
- **v0.3.0** — Site Knowledge 핵심 기능, Chrome profile 격리, CDP 완전 제거
- **v0.4.0** — 멀티 세션 지원 (동적 포트 + 세션별 프로필 격리 + Extension 포트 주입)

---

## 9. 향후 계획

- 테스트 코드 작성 (CDP 제거 후 기존 테스트 삭제됨 — 새 테스트 필요)
- Headless 모드 지원 (CI/CD 환경에서의 자동화)
- 네트워크 요청 인터셉트 기능
- 스크린샷 / PDF 캡처 기능
- npm 패키지 배포

---

**GitHub**: AlyBrowser Repository
**최종 업데이트**: 2026.03.06
