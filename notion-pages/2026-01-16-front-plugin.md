# 2026.01.16 Front Plugin - Figma MCP 데이터 정제 플러그인

## 개요

Front Plugin은 Figma MCP에서 가져온 디자인 데이터를 프로젝트 컨벤션에 맞게 정제하여 정확한 코드를 생성하는 Claude Code 플러그인입니다.

---

## 목차

1. 플러그인 소개
2. 해결하는 문제
3. 설치 방법
4. 사용 가능한 명령어
5. 작동 방식
6. 토큰 자동 생성
7. 정제 규칙
8. 지원 환경

---

## 1. 플러그인 소개

### 주요 특징

- PostToolUse Hook 기반 자동 정제
- 프로젝트 설정 자동 감지
- 디자인 토큰 자동 생성
- 중복 검사 및 관리

### 핵심 기능

- 색상 매핑: Figma 색상 → 디자인 토큰
- 사이즈 정규화: px 값 → spacing 토큰
- 폰트 스타일: Figma typography → 프로젝트 시스템
- 컴포넌트 매칭: 기존 컴포넌트 재사용
- 에셋 네이밍: 파일명 규칙 적용

---

## 2. 해결하는 문제

Figma MCP에서 디자인 데이터를 가져올 때 발생하는 문제들:

- 색상 매핑 오류: Figma 색상이 프로젝트 디자인 토큰과 매칭 안됨
- 사이즈/간격 불일치: px 값을 그대로 사용하여 spacing 토큰과 불일치
- 폰트 스타일 오류: Figma typography가 프로젝트 시스템과 맞지 않음
- 컴포넌트 인식 실패: 기존 컴포넌트를 재사용하지 않고 새로 생성
- 이미지/벡터 파일 문제: 파일명이 임의로 지정됨

---

## 3. 설치 방법

```bash
# 플러그인 디렉토리로 이동
cd /path/to/front-plugin

# Claude Code에서 플러그인 활성화
claude --plugin-dir /path/to/front-plugin
```

---

## 4. 사용 가능한 명령어

| 명령어 | 설명 |
|--------|------|
| `/front-plugin:figma-setup` | 프로젝트 설정 초기화 |
| `/front-plugin:figma-sync` | 토큰/컴포넌트 캐시 업데이트 |
| `/front-plugin:figma-to-code` | Figma 디자인을 코드로 변환 |

---

## 5. 작동 방식

```
[Figma MCP 호출] → [PostToolUse Hook] → [데이터 정제] → [코드 생성]
                          ↓
                  색상 → 디자인 토큰
                  사이즈 → spacing 토큰
                  폰트 → typography 토큰
                  컴포넌트 → 기존 컴포넌트 매칭
                  이미지 → 파일명 규칙 적용
```

### 설정 파일

`.claude/front-plugin.local.md`에서 프로젝트 설정을 관리합니다:

```yaml
---
framework: react
styling: design-tokens
fallback_styling: tailwind
design_tokens_path: src/styles/tokens.css
component_paths:
  - src/components
image_path: public/assets/images
icon_path: public/assets/icons
naming_convention: kebab-case
base_spacing: 4
---
```

---

## 6. 토큰 자동 생성

매칭되는 디자인 토큰이 없을 경우 자동으로 새 토큰을 생성합니다.

### 작동 방식

```
Figma 값 → 기존 토큰 검색
              ↓
    Delta E < 5: 정확한 매칭 (기존 토큰 사용)
    Delta E 5-10: 근사 매칭 (기존 토큰 + 경고)
    Delta E > 10: 새 토큰 생성 → 중복 확인 → 파일에 추가
```

### 설정 옵션

| 옵션 | 설명 | 기본값 |
|------|------|--------|
| `auto_generate_tokens` | 토큰 자동 생성 활성화 | `true` |
| `token_generation_threshold` | 새 토큰 생성 임계값 (Delta E) | `10` |
| `duplicate_check_enabled` | 중복 검사 활성화 | `true` |

---

## 7. 정제 규칙

### 색상 매핑

| Figma | 정제 결과 |
|-------|-----------|
| `#3B82F6` | `--color-primary-500` / `bg-primary-500` |
| `#E5E7EB` | `--color-gray-200` / `border-gray-200` |

### 사이즈 정규화

| Figma | 정제 결과 |
|-------|-----------|
| `16px` | `spacing-4` / `p-4` |
| `8px` | `spacing-2` / `gap-2` |

### 컴포넌트 매칭

| Figma Component | 매칭 결과 |
|-----------------|-----------|
| `Button / Primary / Large` | `<Button variant="primary" size="lg">` |
| `Input / Default` | `<Input />` |

---

## 8. 지원 환경

### 프레임워크

- React / Next.js
- Vue / Nuxt
- Svelte

### 스타일링

- Design Tokens (CSS Variables, TypeScript)
- Tailwind CSS
- CSS-in-JS (styled-components, emotion)
- Sass/SCSS

### 요구사항

- Claude Code CLI
- Figma MCP (공식 또는 Figma-Context-MCP)

---

## 참고 자료

- License: MIT
- Author: choiwon

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-01-16
- 플러그인 버전: 0.1.0
