# 2026.01.21 Code Review Plugin - Claude Code 플러그인

## 개요

Code Review Plugin은 Claude Code 플러그인으로, 4개의 전문 에이전트가 병렬로 코드를 분석하여 개발자와 QA 모두에게 유용한 리뷰를 제공합니다.

---

## 목차

1. 플러그인 정보
2. 설치 방법
3. 에이전트 구성
4. 사용 방법
5. 출력 형식
6. 디렉토리 구조

---

## 1. 플러그인 정보

- 이름: review-report
- 버전: 1.0.0
- 위치: /Users/choiwon/business/common/plugins/review-report
- 라이선스: MIT
- GitHub: https://github.com/k984530/code-review-plugin

---

## 2. 설치 방법

### 플러그인 디렉토리에 복사

```bash
cp -r review-report ~/.claude/plugins/
```

### Claude Code에서 직접 로드

```bash
claude --plugin-dir /path/to/review-report
```

---

## 3. 에이전트 구성

### 3.1 Screen Component Reviewer (페이지별 변경사항)

- 대상: QA, 기획자
- 역할: 유저 관점에서 페이지를 분류하고, 각 페이지 내 변경된 컴포넌트와 기능을 분석
- 출력: 페이지별 변경 컴포넌트 목록, 변경 전/후 비교

### 3.2 Side Effect Reviewer (부작용 분석)

- 대상: 개발자, QA
- 역할: 코드 변경으로 인해 발생할 수 있는 Side Effect 분석
- 출력: 주의 필요 Side Effect, 영향받는 코드/기능, 체크리스트

### 3.3 Code Quality Reviewer (코드 품질)

- 대상: 개발자
- 역할: 코드 품질, 패턴, 잠재적 이슈 분석 및 개선점 제안
- 출력: 개선 필요/권장 항목, 코드 예시, 품질 지표

### 3.4 QA Checklist Reviewer (테스트 체크리스트)

- 대상: QA
- 역할: 이번 작업에서 QA가 확인해야 할 테스트 항목 생성
- 출력: 기능/입력값/UI/시나리오별 테스트 체크리스트

---

## 4. 사용 방법

### 커맨드

```bash
# 전체 리뷰 실행 (4개 에이전트 병렬)
/review-report

# 특정 경로 리뷰
/review-report --target src/components

# 코드 품질만 리뷰 (개발자용)
/review-report --type quality

# QA 체크리스트만 생성
/review-report --type qa

# 특정 경로의 Side Effect 분석
/review-report --target src/hooks --type side-effect
```

### 옵션 설명

| 옵션 | 설명 |
|------|------|
| --target <path> | 리뷰 대상 경로 지정 |
| --type screen | 페이지별 컴포넌트/기능 변경사항 |
| --type side-effect | Side Effect 분석 |
| --type quality | 코드 품질 리뷰 (개발자용) |
| --type qa | QA 테스트 체크리스트 |

---

## 5. 출력 형식

리뷰 완료 시 다음 항목을 포함한 종합 리포트가 생성됩니다:

- 페이지별 변경사항
- Side Effect 주의사항
- 코드 품질 피드백 (개발자용)
- QA 테스트 체크리스트

---

## 6. 디렉토리 구조

```
review-report/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── agents/
│   ├── screen-component-reviewer.md
│   ├── side-effect-reviewer.md
│   ├── code-quality-reviewer.md
│   └── qa-checklist-reviewer.md
├── commands/
│   └── review-report.md
├── skills/
│   └── code-review-guide/
│       └── SKILL.md
└── README.md
```

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-21
- 버전: 1.0

