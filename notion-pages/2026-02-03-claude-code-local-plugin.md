# 2026.02.03 Claude Code 로컬 플러그인 등록 - 정확한 방법 가이드

## 개요

Claude Code에서 로컬 플러그인을 등록하고 활성화하는 정확한 방법을 안내합니다. 로컬 마켓플레이스를 통해 직접 개발한 플러그인을 Claude Code에 연결할 수 있습니다.

---

## 목차

1. Step 1: marketplace.json 생성
2. Step 2: settings.json에 마켓플레이스 등록
3. Step 3: 플러그인 활성화
4. 주의사항

---

## Step 1: marketplace.json 생성

플러그인 루트 디렉토리에 .claude-plugin/marketplace.json 파일을 생성합니다.

### 디렉토리 구조

```
~/business/DesignPlugin/
├── .claude-plugin/
│   └── marketplace.json    ← 이 파일 생성
└── pencil-design/
    └── .claude-plugin/
        └── plugin.json
```

### marketplace.json 내용

```json
{
  "name": "local-plugins",
  "owner": {
    "name": "Your Name"
  },
  "plugins": [
    {
      "name": "pencil-design",
      "source": "./pencil-design",
      "description": "Pencil.dev 디자인 워크플로우 플러그인"
    }
  ]
}
```

---

## Step 2: settings.json에 마켓플레이스 등록

~/.claude/settings.json 파일에 로컬 마켓플레이스를 등록합니다.

```json
{
  "extraKnownMarketplaces": {
    "local-plugins": {
      "source": {
        "source": "directory",
        "path": "/Users/choiwon/business/DesignPlugin"
      }
    }
  },
  "enabledPlugins": {
    "pencil-design@local-plugins": true
  }
}
```

---

## Step 3: 플러그인 활성화

Claude Code에서 플러그인을 활성화합니다.

```bash
# Claude Code 실행 후
/plugin

# 또는 CLI로 직접
/plugin install pencil-design@local-plugins
/plugin enable pencil-design@local-plugins
```

---

## ⚠️ 주의사항

🚨 **절대 경로 필수**: path에는 반드시 절대 경로 사용 (~ 대신 /Users/choiwon/...)

📌 **플러그인 이름 형식**: 플러그인명@마켓플레이스명 (예: pencil-design@local-plugins)

📁 **marketplace.json 위치**: 플러그인들의 상위 폴더의 .claude-plugin/ 안에 위치

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-02-03
- 버전: 1.0
