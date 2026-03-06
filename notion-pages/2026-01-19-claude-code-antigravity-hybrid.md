# 2026.01.19 Claude Code + Antigravity - 하이브리드 AI 개발 워크플로우 가이드

## 개요

Google Antigravity와 Claude Code를 함께 사용하면 계획과 실행을 분리한 효율적인 AI 기반 개발이 가능합니다. 이 문서는 두 도구의 통합 방법과 하이브리드 워크플로우를 상세히 다룹니다.

---

## 목차

1. 도구 소개
2. 왜 하이브리드인가?
3. 통합 방법
4. 하이브리드 워크플로우
5. antigravity-claude-proxy 설정
6. 실전 팁과 베스트 프랙티스
7. 주의사항
8. 참고 자료

---

## 1. 도구 소개

### Google Antigravity

Google이 2025년 11월 Gemini 3와 함께 발표한 AI 기반 IDE입니다.

**주요 특징:**
- Agent-First 철학: AI를 자율적인 개발 주체로 취급
- Gemini 3 Pro/Flash 내장
- Editor View와 Manager View 이중 인터페이스
- 다중 에이전트 병렬 실행 지원
- Claude Sonnet 4.5, Claude Opus 4.5 모델 지원
- VS Code 포크 기반, 확장 프로그램 호환

**가격:** 개인 사용자 무료 (Public Preview)

### Claude Code

Anthropic의 터미널 기반 AI 개발 도구입니다.

**주요 특징:**
- CLI 기반 인터페이스
- Claude Opus 4.5, Sonnet 4.5 모델 지원
- Plan Mode로 계획/실행 분리
- 코드베이스 전체 분석 및 리팩토링
- Git 워크트리 통합 지원

**가격:** 유료 구독 (Pro, Team, Enterprise)

### 비교 요약

| 항목 | Antigravity | Claude Code |
|------|-------------|-------------|
| 인터페이스 | IDE (GUI) | CLI (터미널) |
| 강점 | 계획, 리서치, 오케스트레이션 | 코드 실행, 정밀 구현 |
| 모델 | Gemini 3, Claude, GPT-OSS | Claude 전용 |
| 가격 | 무료 | 유료 |

---

## 2. 왜 하이브리드인가?

### 역할 분담의 이점

두 도구는 서로 다른 강점을 가지고 있습니다:

- **Antigravity (Gemini 3 Pro)**: 구조, 아키텍처, 작업 관리, 웹 리서치
- **Claude Code (Claude Opus)**: 로직, 알고리즘, 정밀 구현

### 토큰 효율성

Gemini 크레딧을 계획 단계에 사용하고, Claude 크레딧을 코드 실행에 집중하면 전체 비용을 최적화할 수 있습니다.

### 품질 보장

두 AI가 서로의 작업을 검증하는 구조로, 버그가 줄고 코드 품질이 향상됩니다.

---

## 3. 통합 방법

### 방법 1: Antigravity에 Claude Code 확장 설치

Antigravity는 VS Code 포크이므로 Claude Code 확장을 직접 설치할 수 있습니다.

```bash
# Antigravity 내 확장 프로그램에서 Claude Code 검색 후 설치
```

**장점:**
- 단일 환경에서 두 도구 사용
- 내장 에이전트 한도 소진 시 Claude Code로 전환 가능

### 방법 2: antigravity-claude-proxy 사용

Antigravity의 무료 Claude/Gemini 모델을 Claude Code CLI에서 사용합니다.

```bash
# 프록시 설치 및 실행
npm install -g antigravity-claude-proxy
antigravity-claude-proxy start
```

**장점:**
- Claude Code 유료 구독 없이 사용 가능
- Claude Opus 4.5 Thinking 모델 무료 사용

### 방법 3: 별도 환경에서 연동

각 도구를 독립적으로 실행하고 파일을 통해 연동합니다.

```
[Antigravity] → plan.md 생성 → [Claude Code] 참조하여 실행
```

**장점:**
- 각 도구의 고유 기능 최대 활용
- 명확한 단계 구분

---

## 4. 하이브리드 워크플로우

### 6단계 워크플로우

#### 1단계: 계획 수립 (Antigravity + Gemini 3 Pro)

```
프롬프트: "Create a detailed implementation plan for [기능 설명].
Research best practices and create a comprehensive roadmap.
DO NOT write any code yet."
```

Gemini가 웹 리서치를 수행하고 구현 계획을 작성합니다.

#### 2단계: 계획 검토 및 개선

```
프롬프트: "Improve the implementation plan based on the following feedback:
[피드백 내용]. Still DO NOT write code."
```

계획을 반복적으로 개선합니다.

#### 3단계: 가이드라인 문서 내보내기

```
프롬프트: "Export this plan as a markdown document named 'roadmap.md'
with clear implementation steps."
```

Claude Code가 참조할 계획 문서를 생성합니다.

#### 4단계: Claude Code에서 계획 감사

Claude Code Plan Mode에서:

```
프롬프트: "Please audit @roadmap.md and let me know what the
implementation steps are, or if you suggest any changes.
Be thorough, yet concise with your audit."
```

#### 5단계: 실행 (Claude Code + Claude Opus)

```
프롬프트: "PROCEED with roadmap as written."
```

Claude가 계획에 따라 코드를 구현합니다.

#### 6단계: 디버깅 및 검증

```
프롬프트: "Fix all above errors. Find and fix all related issues."
```

---

## 5. antigravity-claude-proxy 설정

### 설치

```bash
# NPM으로 설치
npm install -g antigravity-claude-proxy

# 또는 npx로 직접 실행
npx antigravity-claude-proxy@latest start
```

### Google 계정 연결

```bash
# CLI로 계정 추가
antigravity-claude-proxy accounts add

# 또는 웹 대시보드 사용
# http://localhost:8080 접속 후 계정 탭에서 추가
```

### Claude Code 설정

`~/.claude/settings.json` 파일 생성/수정:

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "test",
    "ANTHROPIC_BASE_URL": "http://localhost:8080",
    "ANTHROPIC_MODEL": "claude-opus-4-5-thinking",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-opus-4-5-thinking",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "claude-sonnet-4-5-thinking",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "claude-sonnet-4-5",
    "CLAUDE_CODE_SUBAGENT_MODEL": "claude-sonnet-4-5-thinking"
  }
}
```

### 환경 변수 설정

**macOS/Linux:**

```bash
echo 'export ANTHROPIC_BASE_URL="http://localhost:8080"' >> ~/.zshrc
echo 'export ANTHROPIC_API_KEY="test"' >> ~/.zshrc
source ~/.zshrc
```

**Windows (PowerShell):**

```powershell
Add-Content $PROFILE "`$env:ANTHROPIC_BASE_URL = 'http://localhost:8080'"
Add-Content $PROFILE "`$env:ANTHROPIC_API_KEY = 'test'"
. $PROFILE
```

### 지원 모델

**Claude 모델:**
- claude-opus-4-5-thinking
- claude-sonnet-4-5-thinking
- claude-sonnet-4-5

**Gemini 모델:**
- gemini-3-flash
- gemini-3-pro-low
- gemini-3-pro-high

### 사용 방법

```bash
# 터미널 1: 프록시 시작
antigravity-claude-proxy start

# 터미널 2: Claude Code 실행
claude

# 모델 전환
/model
```

---

## 6. 실전 팁과 베스트 프랙티스

### 프롬프트 작성 팁

**구체적으로 지시하기:**
```
# 나쁜 예
"make this better"

# 좋은 예
"Follow the exact file structure from the Gemini plan.
Use the same naming conventions as specified in roadmap.md"
```

**단계별 진행:**
```
# 계획 단계
"Research and plan only. DO NOT write code."

# 실행 단계
"Implement exactly as planned. Reference @roadmap.md"
```

### 토큰 절약 전략

1. **계획은 Gemini로**: 웹 리서치와 계획 수립에 Gemini 크레딧 사용
2. **실행은 Claude로**: 코드 생성에 Claude 크레딧 집중
3. **Google AI Studio 활용**: 계획 프로토타입은 무료 AI Studio에서 작성

### 버전 관리

```bash
# 각 단계마다 커밋
git add . && git commit -m "Phase 1: Planning complete"

# 롤백 가능하도록 자주 커밋
"After completing each step, commit the changes so we can rollback if needed."
```

### 다중 계정 활용

```bash
# 여러 Google 계정 추가로 레이트 제한 우회
antigravity-claude-proxy accounts add  # 계정 1
antigravity-claude-proxy accounts add  # 계정 2

# 로드 밸런싱 전략 선택
# - Hybrid: 자동 전환
# - Sticky: 계정 고정
# - Round-Robin: 순차 사용
```

---

## 7. 주의사항

### 레이트 제한

- Antigravity 무료 버전은 일일/주간 사용량 제한 있음
- Google One 구독자는 더 높은 제한 적용
- 다중 계정으로 제한 완화 가능

### 보안 고려사항

- 민감한 데이터가 포함된 프로젝트는 주의 필요
- 프록시 서버는 로컬에서만 실행 권장
- 기업 환경에서는 데이터 레지던시 정책 확인

### 모델 특성 이해

- **Gemini 3 Pro**: 컨텍스트 이해와 계획에 강함
- **Claude Opus 4.5**: 정밀한 코드 생성에 강함
- 각 모델의 강점에 맞는 작업 배분 필요

### Antigravity 현재 한계

- 아직 퍼블릭 프리뷰 단계
- 속도가 느리다는 피드백 있음
- 일부 워크플로우에서 Claude Code의 Plan Mode가 더 정교함

---

## 8. 관련 도구

### antigravity-claude-proxy

Antigravity 모델을 Claude Code에서 사용할 수 있게 해주는 프록시 서버.

- GitHub: https://github.com/badrisnarayanan/antigravity-claude-proxy

### opencode-antigravity-auth

OpenCode에서 Antigravity OAuth 인증을 사용할 수 있게 해주는 플러그인.

- GitHub: https://github.com/NoeFabris/opencode-antigravity-auth

---

## 참고 자료

- Google Antigravity 공식 문서: https://antigravity.google/docs
- Google Developers Blog: https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/
- Claude Code + Antigravity 워크플로우 프롬프트: https://gist.github.com/koezyrs/3720d08fd9987c7df4b46bf01c921fad
- antigravity-claude-proxy 설정 가이드: https://syntackle.com/blog/claude-code-free-using-antigravity-proxy/
- AI Coding Agents 비교 2026: https://www.prismlabs.uk/blog/ai-coding-agents-comparison-2026

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-19
- 버전: 1.0
