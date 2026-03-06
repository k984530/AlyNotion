# 2026.01.20 Claude Code 설치 가이드 - Homebrew부터 JavaScript 설정까지

## 개요

macOS 환경에서 Claude Code CLI를 설치하고 JavaScript 프로젝트와 연동하기 위한 완벽 가이드입니다. Homebrew 설치부터 Node.js 설정까지 모든 단계를 다룹니다.

---

## 목차

1. 사전 요구사항 (Homebrew 설치)
2. Claude Code 설치 방법
3. Node.js 및 npm 설정
4. JavaScript 프로젝트 설정
5. 설치 확인 및 문제 해결

---

## 1. 사전 요구사항 (Homebrew 설치)

macOS에서 패키지 관리를 위해 Homebrew를 먼저 설치합니다.

### Homebrew 설치

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 설치 후 PATH 설정 (Apple Silicon Mac)

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc
```

### 설치 확인

```bash
brew --version
```

---

## 2. Claude Code 설치 방법

Claude Code는 3가지 방법으로 설치할 수 있습니다.

### 방법 1: Native Installer (권장)

가장 간단한 방법으로, Node.js가 필요하지 않습니다.

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### 방법 2: Homebrew 설치

Homebrew를 통한 설치 방법입니다. 자동 업데이트가 되지 않으므로 주기적으로 업그레이드가 필요합니다.

```bash
# 설치
brew install --cask claude-code

# 업그레이드
brew upgrade claude-code
```

### 방법 3: npm 설치

Node.js 18 이상이 필요합니다. sudo를 사용하지 마세요!

```bash
npm install -g @anthropic-ai/claude-code
```

---

## 3. Node.js 및 npm 설정

npm 설치 방법을 사용하거나 JavaScript 프로젝트를 위해 Node.js가 필요합니다.

### Node.js 설치 (Homebrew)

```bash
# Node.js LTS 버전 설치
brew install node@22

# PATH 설정 (필요시)
echo 'export PATH="/opt/homebrew/opt/node@22/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### nvm을 통한 설치 (대안)

여러 버전의 Node.js를 관리해야 할 때 추천합니다.

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 터미널 재시작 후
source ~/.zshrc

# Node.js LTS 설치
nvm install --lts
nvm use --lts
```

### 설치 확인

```bash
node --version   # v22.x.x 이상
npm --version    # 10.x.x 이상
```

---

## 4. JavaScript 프로젝트 설정

Claude Code와 함께 JavaScript/TypeScript 프로젝트를 설정하는 방법입니다.

### 새 프로젝트 시작

```bash
# 프로젝트 디렉토리 생성
mkdir my-project && cd my-project

# package.json 초기화
npm init -y

# TypeScript 설정 (선택)
npm install -D typescript @types/node
npx tsc --init
```

### Claude Code 시작

```bash
# 프로젝트 디렉토리에서 실행
cd my-project
claude

# 또는 특정 파일과 함께 시작
claude --file src/index.ts
```

### CLAUDE.md 설정 파일

프로젝트 루트에 CLAUDE.md 파일을 생성하여 프로젝트 컨텍스트를 제공할 수 있습니다.

```bash
# CLAUDE.md 예시
echo '# My Project

## 기술 스택
- Node.js + TypeScript
- Express.js

## 빌드 명령어
npm run build
npm test' > CLAUDE.md
```

---

## 5. 설치 확인 및 문제 해결

### 설치 확인

```bash
# Claude Code 버전 확인
claude --version

# 설치 상태 진단
claude doctor
```

### 자주 발생하는 문제

"command not found: claude" 오류 해결:

```bash
# PATH 환경변수 확인
echo $PATH

# zshrc에 PATH 추가 (필요시)
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

npm 권한 오류 해결:

```bash
# npm 글로벌 디렉토리 설정
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH="~/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 설치 방법 변경 시

설치 방법을 혼합하지 마세요. 기존 설치를 제거한 후 새 방법으로 설치하세요.

```bash
# npm으로 설치한 경우 제거
npm uninstall -g @anthropic-ai/claude-code

# Homebrew로 설치한 경우 제거
brew uninstall claude-code
```

---

## 참고 자료

- Claude Code 공식 문서: https://docs.claude.com/en/docs/claude-code/setup
- Homebrew Formulae: https://formulae.brew.sh/cask/claude-code
- Node.js 공식 사이트: https://nodejs.org
- nvm GitHub: https://github.com/nvm-sh/nvm

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-20
- 버전: 1.0
