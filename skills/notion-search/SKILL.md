---
name: notion-search
description: Notion 워크스페이스에서 페이지를 검색합니다. "노션 검색", "노션에서 찾아", "Notion search" 등의 요청 시 사용합니다.
argument-hint: [검색어]
disable-model-invocation: true
---

# Notion 페이지 검색

Notion 워크스페이스에서 페이지를 검색한다.

**검색어**: $ARGUMENTS

## 실행 절차

### 1단계: Notion 검색 API 호출

`mcp__notion__API-post-search` 도구를 사용하여 검색한다:

- query: 사용자가 입력한 검색어
- filter: {"property": "object", "value": "page"}
- sort: {"direction": "descending", "timestamp": "last_edited_time"}

### 2단계: 결과 정리

검색 결과에서 다음 정보를 추출하여 표로 정리한다:

| # | 제목 | 마지막 수정 | URL |
|---|------|------------|-----|
| 1 | ... | YYYY-MM-DD | ... |

- 최대 10개까지 표시
- 제목이 없는 페이지는 "(제목 없음)"으로 표시
- URL은 Notion 페이지 링크

### 3단계: 후속 작업 안내

검색 결과를 보여준 후, 사용자에게 가능한 후속 작업을 안내한다:
- 특정 페이지의 내용 읽기
- 페이지 수정/업데이트
- 새 페이지 생성 (`/notion-create` 또는 `/notion-post`)
