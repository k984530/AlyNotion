# 2026.01.24 Google Stitch Plugin - Claude Code에서 Stitch MCP 사용법

## 개요

Google Stitch는 Google Labs에서 만든 AI 기반 UI 디자인 도구로, 텍스트 프롬프트와 이미지를 통해 복잡한 UI 디자인과 프론트엔드 코드를 생성합니다. Stitch MCP(Model Context Protocol) 서버를 Claude Code에 연결하면, CLI 환경에서 직접 UI 디자인을 생성하고 관리할 수 있습니다.

---

## 목차

1. Google Stitch란?
2. Stitch MCP 서버 설치
3. Claude Code에 MCP 연결
4. Stitch MCP 주요 도구
5. Stitch Skills (Agent Skills)
6. Claude Code 전용 Skills
7. 활용 팁

---

## 1. Google Stitch란?

Google Stitch는 Google Labs의 AI 기반 UI 디자인 플랫폼으로, Gemini 2.5 Pro의 멀티모달 능력을 활용하여 자연어 프롬프트로 UI 디자인과 프론트엔드 코드를 생성합니다.

### 핵심 기능

- 텍스트 프롬프트 → UI 디자인 자동 생성
- 이미지 입력 → UI 변환
- HTML/CSS 프론트엔드 코드 자동 출력
- 모바일/데스크톱/태블릿 디바이스 지원
- MCP 프로토콜을 통한 AI 에이전트 연동 지원

공식 사이트: https://stitch.withgoogle.com

---

## 2. Stitch MCP 서버 설치

### 사전 요구사항

- Node.js (npx 사용 가능)
- Google Cloud CLI (gcloud)
- Google Cloud 프로젝트 (Stitch API 활성화 필요)

### 설치 명령어

다음 명령어로 초기 설정을 시작합니다:

```bash
npx @_davideast/stitch-mcp init
```

### 설치 과정

1. Client 선택 - Claude Code를 MCP 클라이언트로 선택
2. Google Cloud CLI - 자동 설치 (미설치 시)
3. 인증 - OAuth 플로우 2회 (사용자 인증 + 애플리케이션 인증정보)
4. 프로젝트 선택 - GCP 프로젝트 지정
5. IAM 구성 - 필요 권한 설정
6. API 활성화 - Stitch API 활성
7. 설정 생성 - MCP 설정 JSON 출력

### 설치 확인

```bash
npx @_davideast/stitch-mcp doctor
```

이 명령어로 Google Cloud CLI, 인증, 자격증명, 프로젝트 설정, API 연결을 검증할 수 있습니다.

---

## 3. Claude Code에 MCP 연결

### 방법 1: .mcp.json 설정 파일

프로젝트 루트에 .mcp.json 파일을 생성하거나 ~/.claude/.mcp.json에 추가:

```json
{
  "mcpServers": {
    "stitch": {
      "command": "npx",
      "args": ["@_davideast/stitch-mcp", "proxy"],
      "env": {
        "STITCH_PROJECT_ID": "your-gcp-project-id"
      }
    }
  }
}
```

### 방법 2: CLI 명령어로 추가

```bash
# 사용자 전역 설정
claude mcp add stitch --scope user -- npx @_davideast/stitch-mcp proxy

# 현재 프로젝트만
claude mcp add stitch --scope local -- npx @_davideast/stitch-mcp proxy

# 팀 공유 (프로젝트 .mcp.json에 저장)
claude mcp add stitch --scope project -- npx @_davideast/stitch-mcp proxy
```

### 환경 변수

- STITCH_PROJECT_ID - GCP 프로젝트 ID 지정
- STITCH_USE_SYSTEM_GCLOUD - 기존 gcloud 설치 사용
- STITCH_HOST - 커스텀 API 엔드포인트 지정

### 연결 확인

```bash
# MCP 서버 목록 확인
claude mcp list

# 특정 서버 테스트
claude mcp get stitch
```

---

## 4. Stitch MCP 주요 도구

MCP 서버가 연결되면 Claude Code에서 다음 도구들을 사용할 수 있습니다:

### generate_screen_from_text

- 기능: 텍스트 프롬프트로 새로운 UI 스크린 생성
- 모델: Gemini 3 Pro 또는 Gemini 3 Flash (기본값)
- 디바이스: MOBILE, DESKTOP, TABLET 선택 가능

### extract_design_context

- 기능: 스크린에서 'Design DNA' 추출 (폰트, 색상, 레이아웃)
- 용도: 디자인 시스템 분석 및 일관성 유지

### fetch_screen_code

- 기능: 스크린의 HTML/CSS 프론트엔드 코드 다운로드
- 용도: 생성된 디자인을 실제 프로젝트에 적용

### fetch_screen_image

- 기능: 스크린의 고해상도 스크린샷 다운로드
- 용도: 디자인 문서화 및 리뷰

### create_project

- 기능: 새로운 Stitch 프로젝트(작업 공간) 생성
- 용도: 새 디자인 프로젝트 시작

---

## 5. Stitch Skills (Agent Skills)

Google Labs에서 공식 제공하는 Agent Skills 라이브러리로, Claude Code와 호환됩니다.

### 설치 방법

```bash
# 사용 가능한 Skills 목록 확인
npx add-skill google-labs-code/stitch-skills --list

# 특정 Skill 설치 (전역)
npx add-skill google-labs-code/stitch-skills --skill react:components --global

# 프로젝트 로컬 설치
npx add-skill google-labs-code/stitch-skills --skill design-md
```

### 주요 Skills

- design-md: Stitch 프로젝트를 분석하여 DESIGN.md 파일 생성 (디자인 시스템 문서화)
- react:components: Stitch 스크린을 React 컴포넌트로 변환 + 유효성 검증
- Validation Skills: Stitch HTML을 다른 UI 프레임워크로 변환 후 구문 검증
- Decoupling Data: 정적 디자인 콘텐츠를 외부 목 데이터 파일로 분리
- Generate Designs: 데이터 세트를 기반으로 새로운 디자인 스크린 생성

### Skill 파일 구조

```
skills/[category]/
├── SKILL.md          — 핵심 에이전트 지시사항
├── scripts/          — 검증 및 네트워킹 도구
├── resources/        — 지식 베이스 및 스타일 가이드
└── examples/         — 참고 구현 예시
```

---

## 6. Claude Code 전용 Skills

MCP Marketplace에서 설치 가능한 Claude Code 전용 Stitch Skills입니다.

### Stitch Prompt Architect

- 기능: 자연어 설명을 Stitch 최적화 프롬프트로 변환
- 용도: UI 스펙 파일이나 제품 요구사항을 Stitch에 적합한 디자인 지시어로 변환
- 특징: 짧고 지시적인 프롬프트 생성, 화면/구조/시각적 계층 중심

### Stitch Mockup Extractor

- 기능: Stitch 프로젝트 URL에서 목업 이미지 추출 및 다운로드
- 용도: 생성된 디자인 에셋을 로컬 개발 환경으로 가져오기
- 요구사항: Playwright 및 인증된 Chrome 브라우저 프로필

---

## 7. 활용 팁

### 작업 흐름 예시

1. Stitch MCP로 프로젝트 생성 (create_project)
2. 텍스트 프롬프트로 UI 스크린 생성 (generate_screen_from_text)
3. 디자인 컨텍스트 추출 (extract_design_context)
4. HTML/CSS 코드 다운로드 (fetch_screen_code)
5. React 컴포넌트로 변환 (react:components Skill)

### 프롬프트 작성 방법

- 짧고 지시적인 문장 사용 (예: "로그인 화면, 이메일과 비밀번호 입력 필드, 파란색 CTA 버튼")
- 화면 구조와 시각적 계층에 집중
- 디바이스 타입을 명시적으로 지정 (MOBILE/DESKTOP/TABLET)
- Stitch Prompt Architect Skill을 활용하면 자동으로 최적화된 프롬프트 생성

### 주의사항

- proxy 모드를 사용하면 토큰 자동 갱신 및 디버그 로그 지원
- generate_screen_from_text는 실행에 수 분이 걸릴 수 있음 (재시도 하지 말 것)
- stitch-skills는 Google 공식 지원 제품이 아닌 실험적 프로젝트

---

## 참고 자료

- Stitch MCP Server: https://github.com/davideast/stitch-mcp
- Stitch Skills (Google Labs): https://github.com/google-labs-code/stitch-skills
- Google Stitch 공식 사이트: https://stitch.withgoogle.com
- Claude Code MCP 문서: https://code.claude.com/docs/en/mcp
- Stitch Prompt Architect: https://mcpmarket.com/tools/skills/stitch-prompt-architect
- Stitch Mockup Extractor: https://mcpmarket.com/tools/skills/stitch-mockup-extractor

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-24
- 버전: 1.0
