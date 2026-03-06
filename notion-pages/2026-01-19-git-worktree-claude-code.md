# 2026.01.19 Git Worktree + Claude Code - 병렬 개발 워크플로우 완벽 가이드

## 개요

Git Worktree와 Claude Code를 연동하면 여러 AI 세션을 동시에 실행하며 병렬 개발이 가능합니다. 이 문서는 설정부터 실전 활용까지 상세히 다룹니다.

---

## 목차

1. Git Worktree 기본 개념
2. Claude Code와의 연동 원리
3. 기본 설정 및 사용법
4. 실전 워크플로우
5. 고급 활용 기법
6. 자동화 스크립트
7. 주의사항 및 문제 해결
8. 참고 자료

---

## 1. Git Worktree 기본 개념

### Git Worktree란?

Git Worktree는 단일 저장소에서 여러 작업 디렉토리를 동시에 관리할 수 있게 해주는 Git 기능입니다. 각 워크트리는 독립된 작업 디렉토리를 가지면서 동일한 Git 히스토리와 원격 연결을 공유합니다.

### 주요 특징

- 저장소를 여러 번 복제할 필요 없음
- 각 워크트리는 서로 다른 브랜치 체크아웃 가능
- 파일 상태가 완전히 격리됨
- Git 히스토리는 모든 워크트리가 공유

### 기본 명령어

```bash
# 새 워크트리 생성 (새 브랜치와 함께)
git worktree add ../project-feature -b feature-branch

# 기존 브랜치로 워크트리 생성
git worktree add ../project-bugfix bugfix-123

# 워크트리 목록 조회
git worktree list

# 워크트리 제거
git worktree remove ../project-feature

# 고아 워크트리 정리
git worktree prune
```

---

## 2. Claude Code와의 연동 원리

### 왜 Worktree인가?

Claude Code는 현재 디렉토리의 코드를 분석하고 수정합니다. 여러 Claude 세션을 동시에 실행할 때 같은 디렉토리를 사용하면 충돌이 발생합니다. Git Worktree를 사용하면:

- 각 Claude 세션이 독립된 디렉토리에서 작업
- 파일 충돌 없이 병렬 개발 가능
- 실패한 실험은 워크트리만 삭제하면 됨
- 여러 구현 방식을 동시에 비교 가능

### 작동 방식

```
메인 저장소 (main branch)
├── worktree-A (feature-a branch) → Claude 세션 1
├── worktree-B (feature-b branch) → Claude 세션 2
└── worktree-C (bugfix branch)    → 개발자 직접 작업
```

---

## 3. 기본 설정 및 사용법

### 3.1 워크트리 생성

```bash
# 기본 형식
git worktree add -b <브랜치명> <디렉토리경로>

# 예시: AI 작업용 워크트리 생성
git worktree add -b ai/payment-feature ../myproject-ai-payment
```

### 3.2 워크트리에서 Claude Code 실행

```bash
# 워크트리로 이동
cd ../myproject-ai-payment

# 의존성 설치 (필수!)
npm install  # 또는 pip install -r requirements.txt 등

# Claude Code 실행
claude
```

### 3.3 작업 완료 후 정리

```bash
# 메인 디렉토리로 이동
cd ../myproject

# 변경사항 병합
git merge ai/payment-feature

# 워크트리 제거
git worktree remove ../myproject-ai-payment

# 브랜치 삭제 (선택)
git branch -d ai/payment-feature
```

---

## 4. 실전 워크플로우

### 4.1 기본 병렬 개발 워크플로우

```bash
# 터미널 1: Feature A 개발
git worktree add -b feature-a ../project-feature-a
cd ../project-feature-a
npm install
claude

# 터미널 2: Bugfix 작업
git worktree add -b bugfix-123 ../project-bugfix
cd ../project-bugfix
npm install
claude

# 터미널 3: 개발자 직접 작업
cd ../project-main
# 일반적인 개발 작업 수행
```

### 4.2 다중 구현 비교 워크플로우

여러 가지 구현 방식을 동시에 테스트할 때 유용합니다.

```bash
# 3가지 결제 게이트웨이 구현 동시 진행
git worktree add -b ai/payments-stripe ../app-ai-stripe
git worktree add -b ai/payments-square ../app-ai-square
git worktree add -b ai/payments-braintree ../app-ai-braintree

# 각 워크트리에서 Claude에게 다른 지시
# 완료 후 최적의 구현 선택하여 병합
```

### 4.3 incident.io 팀의 워크플로우

incident.io 팀은 커스텀 `w` 함수를 사용합니다:

```bash
# 한 줄 명령으로 워크트리 생성 + Claude 실행
w myproject new-feature claude

# 작동 방식:
# 1. 워크트리 자동 생성
# 2. 디렉토리 이동
# 3. Claude Code 즉시 실행
```

---

## 5. 고급 활용 기법

### 5.1 Git Alias 설정

`.gitconfig`에 추가하여 편리하게 사용:

```ini
[alias]
    wt-ai = "!f() { git worktree add -b ai/$1 ../$(basename $(pwd))-ai-$1; }; f"
    wt-ai-rm = "!f() { git worktree remove ../$(basename $(pwd))-ai-$1 && git branch -D ai/$1; }; f"
    wt-list = worktree list
```

사용 예:

```bash
git wt-ai payment-feature    # 워크트리 생성
git wt-ai-rm payment-feature # 워크트리 제거
```

### 5.2 Sparse Checkout과 결합

대규모 모노레포에서 특정 폴더만 체크아웃:

```bash
# 워크트리 생성
git worktree add ../project-api api-feature

# Sparse Checkout 설정
cd ../project-api
git sparse-checkout init
git sparse-checkout set src/api packages/shared

# 필요한 폴더만 체크아웃된 상태로 Claude 실행
claude
```

### 5.3 /worktree 명령어 활용

Claude Code의 `/worktree` 명령어를 사용한 자동화:

```bash
# 3개의 워크트리 순차 생성
/worktree 3

# 대량 작업 시 반복문 사용
for i in $(seq 1 10); do claude ... /worktree; done
```

---

## 6. 자동화 스크립트

### 6.1 워크트리 생성 스크립트

`~/bin/create-ai-worktree.sh`:

```bash
#!/bin/bash
TASK_NAME=$1
REPO_NAME=$(basename $(pwd))
WORKTREE_PATH="../${REPO_NAME}-ai-${TASK_NAME}"

# 워크트리 생성
git worktree add -b "ai/${TASK_NAME}" "${WORKTREE_PATH}"

# 디렉토리 이동
cd "${WORKTREE_PATH}"

# 의존성 설치 (프로젝트 타입에 따라)
if [ -f "package.json" ]; then
    npm install
elif [ -f "requirements.txt" ]; then
    pip install -r requirements.txt
fi

# 환경 파일 복사
if [ -f "../${REPO_NAME}/.env" ]; then
    cp "../${REPO_NAME}/.env" .env
fi

echo "Worktree created at: ${WORKTREE_PATH}"
echo "Run 'claude' to start Claude Code"
```

### 6.2 워크트리 정리 스크립트

`~/bin/cleanup-ai-worktree.sh`:

```bash
#!/bin/bash
TASK_NAME=$1
REPO_NAME=$(basename $(pwd))
WORKTREE_PATH="../${REPO_NAME}-ai-${TASK_NAME}"

# 워크트리 제거
git worktree remove "${WORKTREE_PATH}"

# 브랜치 삭제
git branch -D "ai/${TASK_NAME}"

# 고아 워크트리 정리
git worktree prune

echo "Cleaned up worktree and branch for: ${TASK_NAME}"
```

### 6.3 스크립트 설치

```bash
chmod +x ~/bin/create-ai-worktree.sh
chmod +x ~/bin/cleanup-ai-worktree.sh
export PATH="$HOME/bin:$PATH"  # .bashrc 또는 .zshrc에 추가
```

---

## 7. 주의사항 및 문제 해결

### 7.1 주의사항

- **의존성 설치 필수**: 각 워크트리는 독립적인 `node_modules`, `vendor` 등 필요
- **환경 파일 관리**: `.env` 파일에 실제 자격증명 포함하지 않기
- **정기적 정리**: `git worktree prune`으로 고아 워크트리 정리
- **커밋 규칙**: Claude에게 논리적 체크포인트에서 자주 커밋하도록 지시
- **컨텍스트 관리**: 대화가 길어지면 새 세션 시작 고려

### 7.2 일반적인 문제 해결

**"branch is already checked out" 오류**

```bash
# 해결: 새로운 브랜치 이름 사용
git worktree add -b ai/feature-v2 ../project-feature-v2
```

**의존성 오류**

```bash
# 각 워크트리에서 의존성 재설치
cd ../project-worktree
rm -rf node_modules
npm install
```

**IDE 호환성 문제**

- VS Code: 워크트리 잘 지원
- 다른 IDE: 별도 설정 필요할 수 있음
- 권장: 워크트리 사용 전 IDE 호환성 테스트

### 7.3 성능 최적화

```bash
# 대규모 저장소에서 sparse-checkout 사용
git sparse-checkout init --cone
git sparse-checkout set src/specific-folder

# 불필요한 파일 제외
echo "*.log" >> .git/info/sparse-checkout
```

---

## 8. 관련 도구

### Crystal

병렬 Claude Code 세션 관리 데스크톱 앱:

- 여러 세션 동시 실행
- 각 반복마다 자동 커밋
- 이전 상태로 롤백 가능

GitHub: https://github.com/stravu/crystal

### xlaude

워크트리 네이티브 워크플로우 CLI 도구:

- 자동 브랜치 생성
- 이름 자동 정제
- 서브모듈 업데이트 지원

GitHub: https://github.com/Xuanwo/xlaude

---

## 참고 자료

- Claude Code 공식 문서 - Common Workflows: https://code.claude.com/docs/en/common-workflows
- Git Worktree 공식 문서: https://git-scm.com/docs/git-worktree
- incident.io Blog - Git Worktrees: https://incident.io/blog/shipping-faster-with-claude-code-and-git-worktrees
- DEV Community Guide: https://dev.to/bhaidar/supercharge-your-ai-coding-workflow-a-complete-guide-to-git-worktrees-with-claude-code-60m

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-19
- 버전: 1.0
