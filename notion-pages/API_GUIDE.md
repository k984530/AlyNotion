# Notion API 페이지 생성 가이드

## 개요

이 문서는 Notion API를 사용하여 페이지를 생성하고 내용을 추가하는 방법을 설명합니다.

---

## 설정 정보

### API 토큰 위치

```
.mcp.json (프로젝트 루트)
```

```json
{
  "mcpServers": {
    "notion": {
      "env": {
        "NOTION_TOKEN": "ntn_xxxxx..."
      }
    }
  }
}
```

### 상위 페이지 ID (AX Projects)

```
2cc5ee0f-8429-8084-a75a-fa593dd0b852
```

---

## 페이지 생성 방법

### 1. curl을 사용한 페이지 생성

```bash
TOKEN="ntn_xxxxx..."
PARENT_ID="2cc5ee0f-8429-8084-a75a-fa593dd0b852"

curl -s -X POST 'https://api.notion.com/v1/pages' \
  -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2022-06-28' \
  -d '{
    "parent": {"page_id": "'$PARENT_ID'"},
    "properties": {
      "title": {
        "title": [{"text": {"content": "페이지 제목"}}]
      }
    }
  }'
```

### 2. MCP 도구를 사용한 블록 추가

```
mcp__notion__API-patch-block-children
```

파라미터:
- `block_id`: 페이지 ID
- `children`: 블록 배열 (JSON)

---

## 블록 타입별 JSON 형식

### heading_2 (제목 2)

```json
{
  "type": "heading_2",
  "heading_2": {
    "rich_text": [{"type": "text", "text": {"content": "제목 텍스트"}}]
  }
}
```

### heading_3 (제목 3)

```json
{
  "type": "heading_3",
  "heading_3": {
    "rich_text": [{"type": "text", "text": {"content": "제목 텍스트"}}]
  }
}
```

### paragraph (문단)

```json
{
  "type": "paragraph",
  "paragraph": {
    "rich_text": [{"type": "text", "text": {"content": "문단 내용"}}]
  }
}
```

### bulleted_list_item (글머리 기호 목록)

```json
{
  "type": "bulleted_list_item",
  "bulleted_list_item": {
    "rich_text": [{"type": "text", "text": {"content": "목록 항목"}}]
  }
}
```

### numbered_list_item (번호 목록)

```json
{
  "type": "numbered_list_item",
  "numbered_list_item": {
    "rich_text": [{"type": "text", "text": {"content": "목록 항목"}}]
  }
}
```

### divider (구분선)

```json
{
  "type": "divider",
  "divider": {}
}
```

### code (코드 블록)

```json
{
  "type": "code",
  "code": {
    "rich_text": [{"type": "text", "text": {"content": "코드 내용"}}],
    "language": "bash"
  }
}
```

지원 언어: `bash`, `javascript`, `typescript`, `python`, `json`, `plain text` 등

---

## 전체 워크플로우

### 1단계: 페이지 생성

```bash
# 페이지 생성 후 ID 저장
PAGE_ID=$(curl -s -X POST 'https://api.notion.com/v1/pages' \
  -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2022-06-28' \
  -d '{
    "parent": {"page_id": "2cc5ee0f-8429-8084-a75a-fa593dd0b852"},
    "properties": {
      "title": {"title": [{"text": {"content": "YYYY.MM.DD 제목 - 부제목"}}]}
    }
  }' | jq -r '.id')

echo "Created page: $PAGE_ID"
```

### 2단계: 내용 추가 (MCP 도구 사용)

```
mcp__notion__API-patch-block-children
- block_id: {PAGE_ID}
- children: [블록 배열]
```

### 3단계: 추가 내용 계속 추가

같은 방식으로 `patch-block-children`을 여러 번 호출하여 내용 추가

---

## 주의사항

### MCP post-page 버그

`mcp__notion__API-post-page` 도구는 `parent` 파라미터가 JSON 문자열로 직렬화되는 버그가 있음.
→ **curl 명령어로 페이지 생성 권장**

### 블록 추가는 MCP 정상 작동

`mcp__notion__API-patch-block-children`은 정상 작동함.
→ **페이지 생성 후 내용 추가는 MCP 도구 사용**

---

## 예시: 플러그인 문서 페이지 생성

```bash
#!/bin/bash

TOKEN="<YOUR_NOTION_TOKEN>"
PARENT="2cc5ee0f-8429-8084-a75a-fa593dd0b852"

# 페이지 생성
PAGE_ID=$(curl -s -X POST 'https://api.notion.com/v1/pages' \
  -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2022-06-28' \
  -d "{
    \"parent\":{\"page_id\":\"$PARENT\"},
    \"properties\":{
      \"title\":{\"title\":[{\"text\":{\"content\":\"2026.01.16 My Plugin - 설명\"}}]}
    }
  }" | jq -r '.id')

echo "Created: $PAGE_ID"
```

이후 MCP 도구로 내용 추가:
```
mcp__notion__API-patch-block-children
block_id: {PAGE_ID}
children: [개요, 목차, 본문 블록들...]
```

---

## 참고

- Notion API 버전: 2022-06-28
- API 문서: https://developers.notion.com/reference
