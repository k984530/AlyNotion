# Notion API 토큰 설정

Notion API 토큰을 설정하거나 변경한다.

**입력**: $ARGUMENTS

## 실행 절차

### 1단계: 입력 확인

사용자 입력을 확인한다:

- **토큰이 제공된 경우**: 바로 3단계로 진행
- **토큰 없이 호출된 경우**: 2단계에서 현재 상태를 보여주고, 새 토큰 입력을 요청한다

### 2단계: 현재 설정 확인

`.mcp.json` 파일에서 현재 토큰 상태를 확인한다:

```bash
python3 -c "
import json, os
try:
    data = json.load(open('.mcp.json'))
    token = data.get('mcpServers', {}).get('notion', {}).get('env', {}).get('NOTION_TOKEN', '')
    if token:
        masked = token[:8] + '...' + token[-4:]
        print(f'현재 토큰: {masked}')
    else:
        print('토큰이 설정되어 있지 않습니다.')
except FileNotFoundError:
    print('.mcp.json 파일이 없습니다. 초기 설정이 필요합니다.')
"
```

토큰이 비어있으면 사용자에게 토큰 입력을 요청한다.
토큰이 이미 있으면 마스킹하여 보여주고, 변경할지 물어본다.

### 3단계: 토큰 유효성 검증

제공된 토큰으로 Notion API를 호출하여 유효한지 확인한다:

```bash
curl -s -o /dev/null -w "%{http_code}" 'https://api.notion.com/v1/users/me' \
  -H "Authorization: Bearer <NEW_TOKEN>" \
  -H 'Notion-Version: 2022-06-28'
```

- **200**: 유효한 토큰 → 4단계로 진행
- **401**: 잘못된 토큰 → 사용자에게 알리고 다시 입력 요청
- **기타**: 네트워크 오류 등 → 오류 내용 안내

### 4단계: .mcp.json 업데이트

토큰이 유효하면 `.mcp.json` 파일을 업데이트한다:

파일 경로: 프로젝트 루트의 `.mcp.json`

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "<NEW_TOKEN>"
      }
    }
  }
}
```

**주의**: 기존 `.mcp.json`의 다른 서버 설정이 있다면 보존해야 한다. 반드시 파일을 먼저 읽고 notion 서버의 토큰만 변경한다.

### 5단계: 결과 안내

- 토큰 업데이트 완료 메시지
- 마스킹된 토큰 표시 (앞 8자 + ... + 뒤 4자)
- **중요 안내**: MCP 서버 변경사항 반영을 위해 Claude Code를 재시작해야 할 수 있음

### 참고: 토큰 발급 방법

사용자가 토큰 발급 방법을 모를 경우 안내:
1. https://www.notion.so/my-integrations 접속
2. "New integration" 클릭
3. 이름 입력 후 생성
4. Internal Integration Token 복사 (ntn_ 으로 시작)
5. Notion에서 연결할 페이지를 열고 ... → 연결 → 해당 Integration 추가
