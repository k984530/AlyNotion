# 2026.01.20 Google Antigravity - AI 기반 에이전트 우선 IDE

## 개요

Google Antigravity는 Google에서 개발한 AI 기반 통합 개발 환경(IDE)입니다. 2025년 11월 18일 Gemini 3와 함께 발표되었으며, AI 에이전트가 자율적으로 코딩 작업을 수행하는 '에이전트 우선(Agent-first)' 패러다임을 특징으로 합니다.

---

## 목차

1. 제품 소개
2. 설치 방법
3. 주요 기능
4. 지원 AI 모델
5. 활용 사례

---

## 1. 제품 소개

### 기본 정보

- 개발사: Google (DeepMind)
- 출시일: 2025년 11월 18일 (Public Preview)
- 최신 버전: 1.14.2 (2026년 1월 13일)
- 라이선스: Proprietary (프리뷰 기간 무료)
- 기반: Visual Studio Code Fork

### 지원 플랫폼

- Windows 10 이상 (64-bit)
- macOS Monterey 12 이상
- Linux (glibc 2.28+, glibcxx 3.4.25+)

---

## 2. 설치 방법

공식 웹사이트에서 다운로드 및 설치:

1. https://antigravity.google 접속
2. Download 버튼 클릭
3. 운영체제에 맞는 설치 파일 다운로드
4. 설치 파일 실행 후 안내에 따라 설치 완료

---

## 3. 주요 기능

### Editor View (에디터 뷰)

VS Code와 유사한 일반적인 IDE 인터페이스에 에이전트 사이드바가 추가된 형태입니다.

- Tab 자동완성
- 자연어 코드 명령
- 컨텍스트 인식 에이전트

### Manager View (매니저 뷰)

여러 에이전트를 병렬로 관리하는 컨트롤 센터입니다.

- 여러 워크스페이스에서 동시에 에이전트 실행
- 비동기 작업 실행
- 중앙 집중식 미션 컨트롤

### Artifacts (산출물)

에이전트가 생성하는 검증 가능한 결과물로 사용자 신뢰를 구축합니다.

- 작업 목록
- 구현 계획
- 스크린샷
- 브라우저 녹화

### Cross-surface 에이전트

에디터, 터미널, 브라우저를 동기화하여 제어하는 강력한 개발 워크플로우를 제공합니다.

---

## 4. 지원 AI 모델

### Google Gemini 모델

- Gemini 3 Pro - 주력 모델, 넓은 사용량 제공
- Gemini 3 Deep Think - 복잡한 추론 작업용
- Gemini 3 Flash - 빠른 응답 속도용

### 외부 모델 지원

- Anthropic Claude Sonnet 4.5
- Anthropic Claude Opus 4.5
- GPT-OSS-120B (오픈소스 변형)

---

## 5. 활용 사례

### Frontend 개발자

Browser-in-the-loop 에이전트를 활용하여 반복적인 UX 개발 작업을 자동화합니다.

### Full Stack 개발자

철저한 Artifacts와 포괄적인 검증 테스트로 프로덕션 준비 애플리케이션을 구축합니다.

### Enterprise 개발자

Agent Manager를 사용하여 워크스페이스 간 에이전트를 오케스트레이션하고 컨텍스트 전환을 줄여 운영을 간소화합니다.

---

## 참고 자료

- 공식 웹사이트: https://antigravity.google
- Google Codelabs: https://codelabs.developers.google.com/getting-started-google-antigravity
- Wikipedia: https://en.wikipedia.org/wiki/Google_Antigravity
- The Verge: https://www.theverge.com/news/822833/google-antigravity-ide-coding-agent-gemini-3-pro

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-20
- 버전: 1.0
