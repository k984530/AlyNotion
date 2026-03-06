# AlyNotion

Claude Code에서 Notion 페이지를 생성·검색·관리하는 플러그인입니다.

## 기능

- `/notion-create <제목>` — 날짜+제목 형식의 빈 템플릿 페이지 생성
- `/notion-post <주제>` — 주제에 대한 구조화된 글 작성 후 Notion 게시
- `/notion-search <검색어>` — Notion 워크스페이스 내 페이지 검색
- `/notion-setup [토큰]` — API 토큰 설정 및 검증

## 설치

### 1. 저장소 클론

```bash
git clone <repository-url> AlyNotion
```

### 2. Notion API 토큰 설정

```bash
cd AlyNotion
```

Claude Code를 실행하고 `/notion-setup` 으로 토큰을 설정합니다.

또는 직접 `.mcp.json`을 생성합니다:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "<YOUR_NOTION_TOKEN>"
      }
    }
  }
}
```

### 3. Notion Integration 연결

1. https://www.notion.so/my-integrations 에서 Integration 생성
2. 토큰 복사 (ntn_ 으로 시작)
3. Notion에서 대상 페이지 열기 → ... → 연결 → Integration 추가

### 4. Claude Code에서 사용

```bash
cd AlyNotion
claude
```

## 프로젝트 구조

```
AlyNotion/
├── .claude/
│   └── commands/              # 슬래시 커맨드 정의
│       ├── notion-create.md   # /notion-create
│       ├── notion-post.md     # /notion-post
│       ├── notion-search.md   # /notion-search
│       └── notion-setup.md    # /notion-setup
├── notion-pages/              # 생성된 페이지 아카이브
│   ├── PAGE_GUIDE.md          # 페이지 작성 가이드
│   ├── TEMPLATE.md            # 페이지 템플릿
│   ├── API_GUIDE.md           # Notion API 가이드
│   └── YYYY-MM-DD-*.md        # 생성된 페이지들
├── .mcp.json                  # MCP 서버 설정 (git 제외)
├── .gitignore
├── CLAUDE.md                  # 프로젝트 지침
└── README.md
```

## 페이지 형식

생성되는 페이지는 다음 형식을 따릅니다:

- **제목**: `YYYY.MM.DD 주제명 - 부제목`
- **구조**: 개요 → 목차 → 본문 → 참고 자료 → 작성 노트
- **파일명**: `notion-pages/YYYY-MM-DD-제목.md`

자세한 형식은 `notion-pages/PAGE_GUIDE.md`를 참조하세요.

## 설정

| 항목 | 위치 | 설명 |
|------|------|------|
| API 토큰 | `.mcp.json` | Notion Integration 토큰 |
| 상위 페이지 ID | 커맨드 내부 | 기본값: `2cc5ee0f-8429-8084-a75a-fa593dd0b852` |
| 허용 권한 | `.claude/settings.local.json` | Claude Code 도구 허용 목록 |

## 요구사항

- Claude Code CLI
- Node.js (npx 실행 가능)
- Python 3 (토큰 파싱용)
- Notion API Integration 토큰

## 라이선스

MIT
