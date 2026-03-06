# 2026.01.16 Test Plugin - 플러그인 개발 학습용 템플릿

## 개요

Test Plugin은 Claude Code 플러그인 개발을 학습하기 위한 테스트 플러그인입니다. 모든 주요 컴포넌트(Skills, Commands, Agents, Hooks)의 기본 예제를 포함합니다.

---

## 목차

1. 플러그인 소개
2. 설치 방법
3. 포함된 컴포넌트
4. 사용 방법

---

## 1. 플러그인 소개

### 주요 특징

- 플러그인 구조 학습용 템플릿
- 모든 컴포넌트 타입 예제 포함
- 간단하고 이해하기 쉬운 구현

### 컴포넌트 구성

| 타입 | 개수 | 설명 |
|------|------|------|
| Skills | 1 | test-guide |
| Commands | 2 | hello, info |
| Agents | 1 | plugin-helper |
| Hooks | 1 | SessionStart |

---

## 2. 설치 방법

```bash
# 로컬 테스트
claude --plugin-dir /path/to/test-plugin

# 또는 프로젝트에 복사
cp -r test-plugin /your-project/.claude-plugin/
```

---

## 3. 포함된 컴포넌트

### Skills

- **test-guide**: 테스트 작성 가이드를 제공합니다.

### Commands

- `/test-plugin:hello`: 간단한 인사 메시지를 출력합니다.
- `/test-plugin:info`: 플러그인 정보를 표시합니다.

### Agents

- **plugin-helper**: 플러그인 사용법에 대한 질문에 응답합니다.

### Hooks

- **SessionStart**: 세션 시작 시 환영 메시지를 출력합니다.

---

## 4. 사용 방법

1. `/test-plugin:hello` - 인사 메시지 확인
2. `/test-plugin:info` - 플러그인 정보 확인
3. "테스트 작성 방법" 질문 - test-guide 스킬 트리거
4. "이 플러그인 사용법" 질문 - plugin-helper 에이전트 트리거

---

## 참고 자료

- License: MIT
- Author: choiwon

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-16
- 플러그인 버전: 0.1.0
