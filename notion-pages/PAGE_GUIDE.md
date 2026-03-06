# Notion 페이지 작성 가이드

## 페이지 제목 형식

```
YYYY.MM.DD 주제명 - 부제목
```

예시:
- `2026.01.15 QA Plugin - QA 직군을 위한 Claude Code 플러그인`
- `2026.01.14 Claude Code + Notion Connector 연동 이슈 정리`

---

## 문서 구조

### 필수 섹션

1. **개요** (heading_2)
   - 프로젝트/기능에 대한 간단한 설명
   - 1-2문장으로 핵심 내용 전달

2. **목차** (heading_2 + numbered_list)
   - 문서 내 주요 섹션 나열
   - 긴 문서의 경우 필수

3. **본문 섹션들** (heading_2 + heading_3)
   - 소개/주요 기능
   - 설치 방법
   - 사용 방법
   - 상세 설명

4. **참고 자료** (heading_2 + bulleted_list)
   - Repository URL
   - License
   - Author

5. **작성 노트** (heading_2 + bulleted_list)
   - 작성자
   - 작성일
   - 버전

---

## 마크다운 → Notion 블록 매핑

| 마크다운 | Notion 블록 |
|---------|------------|
| `# 제목` | heading_1 |
| `## 제목` | heading_2 |
| `### 제목` | heading_3 |
| 일반 텍스트 | paragraph |
| `- 항목` | bulleted_list_item |
| `1. 항목` | numbered_list_item |
| `` ```코드``` `` | code |
| `---` | divider |
| `- [ ] 할일` | to_do |
| `> 인용문` | quote |

---

## 섹션 구분 규칙

- 주요 섹션(heading_2) 사이에 `---` (divider) 사용
- 하위 섹션(heading_3)은 divider 없이 연결
- 마지막 섹션 뒤에도 divider 추가하지 않음

---

## 코드 블록 언어

지원되는 주요 언어:
- `bash` - 쉘 명령어
- `javascript` / `typescript`
- `python`
- `json`
- `plain text` - 일반 텍스트/예시

---

## 예시 구조

```markdown
# 2026.01.16 새 프로젝트 - 프로젝트 설명

## 개요

이 프로젝트는 XXX를 위한 도구입니다.

---

## 목차

1. 소개
2. 설치 방법
3. 사용 방법

---

## 1. 소개

### 주요 기능

- 기능 A: 설명
- 기능 B: 설명

---

## 2. 설치 방법

```bash
npm install my-package
```

---

## 참고 자료

- Repository: https://github.com/...
- License: MIT

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-16
```

---

## 파일 명명 규칙

```
YYYY-MM-DD-제목.md
```

예시:
- `2026-01-16-qa-plugin.md`
- `2026-01-15-marketplace-plugin.md`
