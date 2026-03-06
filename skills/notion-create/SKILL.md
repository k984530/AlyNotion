---
name: notion-create
description: Notion에 빈 템플릿 페이지를 생성합니다. "노션 페이지 생성", "노션에 새 페이지", "빈 페이지 만들어" 등의 요청 시 사용합니다.
argument-hint: [제목]
disable-model-invocation: true
---

# Notion 페이지 생성

사용자가 제공한 제목으로 Notion에 새 페이지를 생성한다.

**입력**: $ARGUMENTS

## 실행 절차

### 1단계: 제목 구성

- 오늘 날짜를 `YYYY.MM.DD` 형식으로 앞에 붙인다
- 사용자가 부제목도 제공했다면 `YYYY.MM.DD 주제명 - 부제목` 형식 사용
- 예: 입력 "회의록" → "2026.03.06 회의록"

### 2단계: curl로 페이지 생성

아래 curl 명령어로 페이지를 생성한다. TOKEN은 글로벌 MCP 설정에서 읽는다:

```bash
TOKEN=$(python3 -c "import json; print(json.load(open('/Users/choiwon/.claude/.mcp.json'))['mcpServers']['notion']['env']['NOTION_TOKEN'])")
PARENT_ID="2cc5ee0f-8429-8084-a75a-fa593dd0b852"

PAGE_ID=$(curl -s -X POST 'https://api.notion.com/v1/pages' \
  -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2022-06-28' \
  -d '{
    "parent": {"page_id": "'$PARENT_ID'"},
    "properties": {
      "title": {"title": [{"text": {"content": "제목"}}]}
    }
  }' | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")
```

> MCP `post-page`는 JSON 파싱 버그가 있으므로 반드시 curl 사용

### 3단계: 기본 템플릿 블록 추가

`mcp__notion__API-patch-block-children`를 사용하여 기본 구조를 추가한다:

- heading_2: "개요"
- paragraph: (빈 문단)
- divider
- heading_2: "주요 내용"
- paragraph: (빈 문단)
- divider
- heading_2: "TODO"
- to_do: (빈 체크박스)
- divider
- heading_2: "메모"
- paragraph: (빈 문단)

### 4단계: 결과 출력

페이지 생성 결과를 출력한다:
- 생성된 페이지 제목
- Notion URL (https://www.notion.so/ + PAGE_ID에서 하이픈 제거)
