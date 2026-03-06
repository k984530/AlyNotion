---
name: notion-setup
description: Notion API 토큰을 설정하거나 변경합니다. "노션 토큰 설정", "notion setup", "API 키 변경" 등의 요청 시 사용합니다.
argument-hint: [토큰]
disable-model-invocation: true
---

# Notion API 토큰 설정

Notion API 토큰을 설정하거나 변경한다.

**입력**: $ARGUMENTS

## 설정 파일 위치

- 글로벌: `/Users/choiwon/.claude/.mcp.json`
- 프로젝트: `/Users/choiwon/business/common/AlyNotion/.mcp.json`

## 실행 절차

### 1단계: 입력 확인

사용자 입력을 확인한다:

- **토큰이 제공된 경우**: 바로 3단계로 진행
- **토큰 없이 호출된 경우**: 2단계에서 현재 상태를 보여주고, 새 토큰 입력을 요청한다

### 2단계: 현재 설정 확인

```bash
python3 -c "
import json, os
for path in ['/Users/choiwon/.claude/.mcp.json', '/Users/choiwon/business/common/AlyNotion/.mcp.json']:
    try:
        data = json.load(open(path))
        token = data.get('mcpServers', {}).get('notion', {}).get('env', {}).get('NOTION_TOKEN', '')
        if token:
            masked = token[:8] + '...' + token[-4:]
            print(f'{path}: {masked}')
        else:
            print(f'{path}: 토큰 없음')
    except FileNotFoundError:
        print(f'{path}: 파일 없음')
"
```

### 3단계: 토큰 유효성 검증

```bash
curl -s -o /dev/null -w "%{http_code}" 'https://api.notion.com/v1/users/me' \
  -H "Authorization: Bearer <NEW_TOKEN>" \
  -H 'Notion-Version: 2022-06-28'
```

- **200**: 유효 → 4단계로 진행
- **401**: 잘못된 토큰 → 재입력 요청

### 4단계: 설정 파일 업데이트

토큰이 유효하면 **두 파일 모두** 업데이트한다:
1. `/Users/choiwon/.claude/.mcp.json` (글로벌)
2. `/Users/choiwon/business/common/AlyNotion/.mcp.json` (프로젝트)

**주의**: 기존 파일의 다른 서버 설정이 있다면 보존해야 한다. 반드시 파일을 먼저 읽고 notion 서버의 토큰만 변경한다.

### 5단계: 결과 안내

- 토큰 업데이트 완료 메시지
- 마스킹된 토큰 표시
- **중요**: MCP 서버 반영을 위해 Claude Code 재시작 필요할 수 있음

### 참고: 토큰 발급 방법

1. https://www.notion.so/my-integrations 접속
2. "New integration" 클릭
3. 이름 입력 후 생성
4. Internal Integration Token 복사 (ntn_ 으로 시작)
5. Notion에서 연결할 페이지 → ... → 연결 → Integration 추가
