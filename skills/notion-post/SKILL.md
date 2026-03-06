---
name: notion-post
description: 주제를 받아 구조화된 글을 작성하고 Notion에 게시합니다. "노션에 글 작성", "노션 페이지 작성", "노션에 포스트" 등의 요청 시 사용합니다.
argument-hint: [주제]
disable-model-invocation: true
---

# Notion 글 작성 및 게시

주제를 받아서 구조화된 글을 작성하고 Notion에 게시한다.

**입력**: $ARGUMENTS

## 실행 절차

### 1단계: 제목 결정

- 오늘 날짜를 `YYYY.MM.DD` 형식으로 앞에 붙인다
- 형식: `YYYY.MM.DD 주제명 - 부제목`
- 부제목이 명확하지 않으면 내용에 맞는 부제목을 자동 생성한다

### 2단계: 글 내용 작성

아래 가이드 파일을 읽고 형식에 맞춰 글을 작성한다:
- `/Users/choiwon/business/common/AlyNotion/notion-pages/PAGE_GUIDE.md`
- `/Users/choiwon/business/common/AlyNotion/notion-pages/TEMPLATE.md`

구조:
1. **개요** - 주제에 대한 1-2문장 요약
2. **목차** - 본문 섹션 번호 목록
3. **본문 섹션들** - 주제에 맞는 상세 내용 (heading_2 + heading_3 구조)
4. **참고 자료** - 관련 링크, 레퍼런스
5. **작성 노트** - 작성자: Claude Code, 작성일: 오늘 날짜

### 3단계: curl로 페이지 생성

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

### 4단계: 블록 추가

`mcp__notion__API-patch-block-children`를 사용하여 작성한 내용을 블록으로 추가한다.

블록 타입 매핑:
- `## 제목` → heading_2
- `### 제목` → heading_3
- 일반 텍스트 → paragraph
- `- 항목` → bulleted_list_item
- `1. 항목` → numbered_list_item
- 코드 블록 → code (language 지정)
- `---` → divider
- `- [ ] 할일` → to_do
- `> 인용문` → quote

**주의**: Notion API는 한 번에 최대 100개 블록만 추가 가능. 내용이 많으면 여러 번 나눠서 호출한다.

### 5단계: 로컬 md 파일 저장

작성한 내용을 마크다운 파일로도 로컬에 저장한다:
- 경로: `/Users/choiwon/business/common/AlyNotion/notion-pages/YYYY-MM-DD-제목.md`
- 파일명의 제목은 영문 소문자+하이픈 (한글 가능)

### 6단계: 결과 출력

- 생성된 페이지 제목
- Notion URL
- 로컬 저장 경로
