# 2026.01.21 AI Code Review 도입 회의 - Review Report 플러그인

## 개요

AI Code Review 도입을 위한 회의 내용과 그 결과로 개발된 Review Report 플러그인에 대한 문서입니다. 4개의 전문 에이전트를 통해 체계적인 코드 변경사항 분석을 제공합니다.

---

## 목차

1. 회의 내용 요약
2. Review Report 플러그인 소개
3. 에이전트 구성
4. 사용 방법
5. 향후 계획

---

## 1. 회의 내용 요약

### 1.1 Git Rules (브랜치 관리 정책)

- main 브랜치에 대해 --force push를 금지하는 방향으로 검토
- 현재 Git Rules가 없으므로 구체적인 규칙 정립에 대해서는 추가 논의 필요

### 1.2 QA GitHub 승인 및 상용 배포 조건

QA GitHub 초대 이후, 상용 배포 시 승인 조건에 대한 논의:

- A안: QA 승인 필수 (부재 시/긴급 배포 대응 방안 필요)
- B안: 2명 이상 승인 시 배포 가능
- C안: 현행 방식 유지
- Github 배포 플로우에 대해서는 추후 결정 예정

### 1.3 AI Review 도입 방식

- A안: 개발자가 직접 AI Review 호출 → 결과를 QA에 공유 (초기 도입, 현실적)
- B안: Codex 사용 (월 200달러 비용)
- C안: GitHub Action 기반 Claude Code (프로젝트별 설정 필요)
- D안: GitHub App 방식 (별도 서버 구축 필요)

→ 초기에는 A안을 적용하고, 이후 점진적으로 자동화하는 방향으로 결정

### 1.4 AI Review 고도화 방향

AI Review에서 검토해야 할 주요 항목:

- 화면 단위, 화면 내 컴포넌트, 기능 단위 리뷰
- 사이드 이펙트 리뷰
- 코드 품질 리뷰
- 수정된 컴포넌트의 영향 범위 분석
- QA 체크리스트 기반 검증
- 수정된 코드에 대한 유저 플로우 관점 리뷰

→ QA와 협업하며 진행, Claude Code Plugin 사용 예정

---

## 2. Review Report 플러그인 소개

회의 결과를 바탕으로 개발된 Claude Code 플러그인입니다. 4개의 전문 에이전트가 병렬로 코드를 분석하여 개발자와 QA 모두에게 유용한 리뷰를 제공합니다.

### 플러그인 정보

- 이름: review-report
- 버전: 1.0.0
- 위치: /Users/choiwon/business/common/plugins/review-report
- 라이선스: MIT

### 디렉토리 구조

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
└── README.md
```

---

## 3. 에이전트 구성

회의에서 논의된 AI Review 고도화 방향을 반영하여 4개의 전문 에이전트로 구성되었습니다.

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

### 설치

```bash
# 플러그인 디렉토리에 복사
cp -r review-report ~/.claude/plugins/

# 또는 Claude Code에서 직접 로드
claude --plugin-dir /path/to/review-report
```

### 커맨드 사용

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

- --target <path>: 리뷰 대상 경로 지정
- --type <type>: 특정 리뷰 타입만 실행
  - screen: 페이지별 컴포넌트/기능 변경사항
  - side-effect: Side Effect 분석
  - quality: 코드 품질 리뷰 (개발자용)
  - qa: QA 테스트 체크리스트

---

## 5. 향후 계획

1. 초기 도입 (A안): 개발자가 직접 /review-report 실행 → 결과를 QA에 공유
2. QA와 협업하여 체크리스트 항목 고도화
3. 점진적 자동화 (GitHub Action 또는 GitHub App 방식 검토)
4. Git Rules 정립 후 배포 플로우와 연동

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-21
- 회의일: 2026-01-21
- 버전: 1.0
