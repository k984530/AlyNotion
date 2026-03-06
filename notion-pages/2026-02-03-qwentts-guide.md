# 2026.02.03 QwenTTS - AI 음성 복제 도구 사용 가이드

## 개요

QwenTTS는 Qwen3-TTS 모델을 기반으로 한 AI 음성 복제 도구입니다. 참조 음성의 음색을 추출하여 새로운 텍스트를 동일한 목소리로 생성할 수 있으며, CLI, 웹 UI, 배치 처리 등 다양한 방식을 지원합니다.

---

## 목차

1. 방법 A: CLI 명령어 (voice_clone.py)
2. 방법 B: 웹 UI (app.py)
3. 방법 C: 대본 배치 처리 (batch_dialogue.py)
4. 프로젝트 구조
5. 사용 팁
6. 트러블슈팅

---

## 1. 방법 A: CLI 명령어 (voice_clone.py)

### 환경 활성화

```bash
conda activate qwen3-tts
```

### 단일 문장 생성

```bash
python voice_clone.py \
    --ref ref_audio/상담사_김사라.wav \
    --ref-text "그러면 약간 수분이 부족한 지성이실 수 있거든요?" \
    --text "안녕하세요, 오늘 상담 도와드릴게요." \
    --lang Korean \
    --output output/결과.wav
```

### 여러 문장 배치 생성

```bash
python voice_clone.py \
    --ref ref_audio/상담사_김사라.wav \
    --ref-text "참조 음성 텍스트" \
    --text-file scripts/상담사_lines.txt \
    --output-dir output/
```

### 주요 옵션

- --ref: 참조 음성 파일 경로 (.wav) - 필수
- --ref-text: 참조 음성에서 말하는 텍스트 - 필수
- --text: 생성할 단일 텍스트
- --text-file: 생성할 텍스트 파일 (줄 단위)
- --lang: 언어 (Korean/English/Chinese/Japanese) - 기본: Korean
- --output: 단일 출력 경로 - 기본: output/cloned.wav
- --output-dir: 배치 출력 디렉토리 - 기본: output

### Claude Code로 활용하기

Claude Code에서 간단한 명령어로 음성 복제를 실행할 수 있습니다:

```
# 단일 음성 생성
"김사라 목소리로 '안녕하세요' 음성 만들어줘"

# 배치 생성
"대본 파일로 음성 생성해줘"

# 결과 확인
"output 폴더에 생성된 파일 확인해줘"
```

🤖 Claude Code가 자동으로 `conda run -n qwen3-tts` 환경에서 `voice_clone.py`를 실행합니다.

⚠️ **주의**: 간혹 생성된 음성이 깨지는 경우가 있어 생성 후 품질 확인이 필요합니다.

---

## 2. 방법 B: 웹 UI (app.py)

```bash
conda activate qwen3-tts
python app.py
```

브라우저에서 http://localhost:7860 접속 후:

1. 📎 참조 음성 업로드 (3초 이상)
2. 참조 음성 텍스트 입력 (정확히!)
3. 생성할 텍스트 입력
4. 언어 선택 → 음성 생성 클릭

---

## 3. 방법 C: 대본 배치 처리 (batch_dialogue.py)

대본 폴더의 모든 스크립트를 자동 처리합니다.

```bash
python batch_dialogue.py
```

### 대본 형식 (대본/*.txt)

```
[상담사]: 안녕하세요, 상담 시작하겠습니다.
[고객]: 네, 저는 제모 상담하러 왔어요.
[상담사]: 겨드랑이 제모시군요.
```

### 출력 구조

```
output/대본/
├── 1_초진_상담중단/
│   ├── 김사라/
│   │   ├── 000_상담사.wav
│   │   ├── 001_고객.wav
│   │   └── ...
│   └── 최원/
└── 1_초진_상담중단_김사라.wav  (합본)
```

---

## 4. 프로젝트 구조

```
QwenTTS/
├── setup.sh              # 환경 설치 스크립트
├── requirements.txt      # 의존성 목록
├── voice_clone.py        # CLI 음성 복제
├── app.py                # Gradio 웹 UI
├── batch_dialogue.py     # 대본 배치 처리
├── ref_audio/            # 참조 음성 폴더
│   ├── 상담사_김사라.wav
│   ├── 상담사_최원.wav
│   ├── 내담자_김사라.wav
│   └── 내담자_최원.wav
├── 대본/                 # 대화 대본 (15개)
│   ├── 1_초진_상담중단.txt
│   ├── 2_초진_단순_여드름.txt
│   └── ...
└── output/               # 생성된 음성 저장
```

---

## 5. 사용 팁

⭐ 참조 음성 품질이 핵심: 배경 소음 없는 깨끗한 녹음이 좋은 결과를 만듭니다

⭐ 참조 텍스트 정확성: 실제 음성과 텍스트가 일치해야 음색 추출이 정확합니다

⭐ 배치 처리 시 프롬프트 재사용: create_voice_clone_prompt()로 미리 추출한 프롬프트를 여러 문장에 재사용하면 속도가 향상됩니다

### 권장 설정

- 참조 음성 길이: 최소 3초, 권장 5~10초
- 참조 음성 품질: 노이즈 없는 깨끗한 녹음
- 지원 언어: 한국어, 영어, 중국어, 일본어
- 하드웨어: GPU 권장 (CUDA/MPS), CPU도 가능 (느림)

---

## 6. 트러블슈팅

### 모델 수동 다운로드

```bash
huggingface-cli download Qwen/Qwen3-TTS-12Hz-1.7B-Base --local-dir ./models/Qwen3-TTS-12Hz-1.7B-Base
```

### GPU 메모리 부족 시

```bash
# 환경변수로 CPU 강제
export QWEN_TTS_MODEL="Qwen/Qwen3-TTS-12Hz-1.7B-Base"
```

---

## 참고 자료

- Model: Qwen/Qwen3-TTS-12Hz-1.7B-Base (HuggingFace)
- Framework: Gradio (Web UI)
- Environment: Conda (qwen3-tts)

---

## 작성 노트

- 작성자: Claude Code
- 작성일: 2026-02-03
- 버전: 1.0
