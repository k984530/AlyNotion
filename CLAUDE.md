# AlyNotion 프로젝트 지침

## 개요

AlyNotion은 Claude Code에서 Notion 페이지를 생성·검색·관리하기 위한 플러그인입니다.

## 슬래시 커맨드

| 커맨드 | 용도 | 사용 예시 |
|--------|------|----------|
| `/notion-create` | 빈 템플릿 페이지 생성 | `/notion-create 회의록` |
| `/notion-post` | 주제로 글 작성 후 게시 | `/notion-post QA 자동화 - Playwright 가이드` |
| `/notion-search` | Notion 페이지 검색 | `/notion-search 플러그인` |
| `/notion-setup` | API 토큰 설정/변경 | `/notion-setup ntn_xxx...` |

## Notion 페이지 작성 규칙

**중요**: 사용자가 Notion 페이지 작성을 요청하면, 반드시 아래 파일들을 먼저 읽고 해당 형식에 맞춰 작성하세요.

1. `notion-pages/PAGE_GUIDE.md` - 페이지 작성 가이드 (형식, 구조, 규칙)
2. `notion-pages/TEMPLATE.md` - 페이지 템플릿
3. `notion-pages/API_GUIDE.md` - Notion API를 통한 직접 페이지 생성 가이드

### 페이지 생성 방식

**권장: API 직접 생성 방식**
1. curl 명령어로 페이지 생성
2. MCP `patch-block-children` 도구로 내용 추가

> MCP `post-page` 도구는 JSON 파싱 버그가 있어 curl 사용 권장

### 트리거 키워드

다음 키워드가 포함된 요청 시 위 파일들을 참조:
- "노션 페이지 작성", "Notion 페이지 작성"
- "노션에 페이지 추가", "노션 문서 작성"
- "페이지를 노션에"

### 작성 후 저장 위치

작성한 md 파일은 `notion-pages/` 디렉토리에 저장:
```
notion-pages/YYYY-MM-DD-제목.md
```

### 페이지 제목 형식

```
YYYY.MM.DD 주제명 - 부제목
```

## 설정

### API 토큰

`.mcp.json` 파일에 Notion API 토큰이 설정되어 있어야 합니다.
토큰 설정은 `/notion-setup` 커맨드를 사용하세요.

### 상위 페이지

기본 상위 페이지: AX Projects (`2cc5ee0f-8429-8084-a75a-fa593dd0b852`)

# currentDate
Today's date is 2026-03-06.
