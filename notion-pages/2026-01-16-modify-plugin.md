# 2026.01.16 Modify Plugin - 다관점 프로젝트 분석 플러그인

## 개요

modify-plugin은 프로젝트를 6가지 전문 관점에서 병렬 분석하여 아이디어 기반 개선점을 도출하는 Claude Code 플러그인입니다. 코드 수정이 아닌 비즈니스/사용자 관점의 개선 방향을 제시합니다.

---

## 목차

1. 플러그인 소개
2. 설치 방법
3. 사용 가능한 명령어
4. 6개 분석 에이전트
5. 분석 결과 형식
6. 플러그인 구조

---

## 1. 플러그인 소개

### 주요 특징

- 6개의 전문 분석 에이전트: 다양한 관점에서 동시 분석
- 병렬 실행: 빠른 종합 분석 결과 도출
- 웹 검색 통합: 최신 트렌드와 경쟁사 분석
- 아이디어 중심: 코드가 아닌 비즈니스/사용자 관점 개선

### 핵심 질문

각 에이전트는 다음 핵심 질문에 답합니다:

- UX: 사용자가 원하는 것을 쉽게 얻는가?
- 비즈니스: 어떤 가치를 만들어내는가?
- 혁신: 새로운 가능성은 무엇인가?
- 단순화: 무엇을 제거할 수 있는가?
- 사용자 목소리: 실제로 뭐라고 말하는가?
- 리스크: 무엇이 잘못될 수 있는가?

---

## 2. 설치 방법

```bash
claude /plugin install /path/to/modify-plugin
```

---

## 3. 사용 가능한 명령어

### 종합 분석 (6개 관점 병렬 실행)

```
/analyze
```

모든 에이전트를 병렬로 실행하여 종합 분석 결과를 도출합니다.

### 개별 관점 분석

```
/analyze-ux          # UX 분석
/analyze-business    # 비즈니스 가치 분석
/analyze-innovation  # 혁신/트렌드 분석
/analyze-simplicity  # 단순화 분석
/analyze-feedback    # 사용자 피드백 분석
/analyze-risk        # 리스크 분석
```

---

## 4. 6개 분석 에이전트

| 에이전트 | 관점 | 분석 영역 |
|----------|------|----------|
| user-experience-analyst | UX | 사용자 흐름, 직관성, 피드백, 접근성 |
| business-value-analyst | 비즈니스 | 수익성, 시장 적합성, 성장 가능성 |
| innovation-scout | 혁신 | 신기술 적용, 트렌드 분석, 차별화 포인트 |
| simplicity-auditor | 단순화 | 복잡도 제거, 프로세스 간소화, 효율화 |
| user-voice-collector | 사용자 목소리 | 피드백 수집, 불만 사항, 요구사항 분석 |
| risk-assessor | 리스크 | 기술 부채, 의존성, 보안 취약점 |

---

## 5. 분석 결과 형식

### 종합 분석 결과 구조

분석이 완료되면 `./analysis-reports/` 디렉토리에 결과가 저장됩니다:

- ux-analysis.md
- business-analysis.md
- innovation-analysis.md
- simplicity-analysis.md
- user-feedback-analysis.md
- risk-analysis.md
- comprehensive-analysis.md (종합 리포트)

### 종합 리포트 포함 내용

- Top 5 우선 개선 과제 (관련 관점, 기대 효과 포함)
- 가장 혁신적인 아이디어 Top 3
- Quick Wins (빠른 성과)
- 로드맵 제안 (즉시/단기/중기)

---

## 6. 플러그인 구조

```
modify-plugin/
├── .claude-plugin/
│   └── plugin.json
├── agents/
│   ├── user-experience-analyst.md
│   ├── business-value-analyst.md
│   ├── innovation-scout.md
│   ├── simplicity-auditor.md
│   ├── user-voice-collector.md
│   └── risk-assessor.md
├── commands/
│   ├── analyze.md
│   ├── analyze-ux.md
│   ├── analyze-business.md
│   ├── analyze-innovation.md
│   ├── analyze-simplicity.md
│   ├── analyze-feedback.md
│   └── analyze-risk.md
├── skills/
│   └── idea-analysis/
│       └── SKILL.md
├── hooks/
│   └── hooks.json
└── README.md
```

---

## 참고 자료

- License: MIT
- Author: choiwon

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-16
- 플러그인 버전: 0.1.0
