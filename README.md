# 🎨 AI Image Editor

> **SAM + Stable Diffusion + GPT API + Streamlit**를 결합한 AI 이미지 편집기입니다.
> 사용자가 이미지를 업로드하고 편집하고 싶은 영역을 선택하면, 해당 영역을 **자동 제거(Inpainting)** 하거나 **텍스트 기반으로 원하는 모습으로 수정(Image Editing)** 할 수 있습니다.

---

## 📌 1. Project Overview

이 프로젝트는 사용자가 직접 복잡한 포토샵 작업을 하지 않아도,
**클릭 기반 영역 선택 + 생성형 AI 기반 편집**만으로 이미지를 쉽게 수정할 수 있도록 만든 웹 데모입니다.

전체 흐름은 다음과 같습니다.

* 🖼️ 사용자가 이미지를 업로드
* ✍️ SAM 기반으로 편집할 영역 마스크 생성
* 🧹 선택 영역 제거(Inpainting)
* 💬 텍스트를 입력해 선택 영역 편집(Image Editing)
* 🤖 GPT API로 입력 문장을 Stable Diffusion용 프롬프트로 보정
* 🎨 Stable Diffusion Inpainting 모델로 최종 이미지 생성

즉, 이 프로젝트는 단순한 이미지 업로더가 아니라,
**영역 선택 → 프롬프트 보정 → 생성형 편집**까지 연결되는 통합형 AI 편집기입니다.

---

## ❓ 2. Why This Project?

이미지 편집은 보통 다음과 같은 어려움이 있습니다.

* 사용자가 직접 편집 영역을 정교하게 지정하기 어렵다
* 자연어로 원하는 수정 내용을 정확하게 프롬프트로 쓰기 어렵다
* 전통적인 편집 툴은 사용 난도가 높다

이 프로젝트는 이런 문제를 해결하기 위해 세 가지 AI 기능을 결합했습니다.

### ✨ 해결하고자 한 문제

* **어디를 편집할지** → SAM으로 해결
* **어떻게 바꿀지** → Stable Diffusion으로 해결
* **프롬프트를 어떻게 잘 쓸지** → GPT API로 해결

즉, 사용자는 이미지를 올리고 클릭하거나 간단한 문장만 입력하면 되고,
시스템이 그 입력을 더 편집 친화적인 형태로 변환하여 결과를 생성하도록 설계했습니다.

---

## 🧠 3. Core Idea

### 1) 🎯 Region Selection with SAM

이미지 편집에서 가장 먼저 필요한 것은 **어느 영역을 수정할지 정확히 지정하는 것**입니다.
이 프로젝트는 Segment Anything Model(SAM)을 활용해 사용자의 클릭이나 포인트 입력으로 마스크를 생성합니다.

* Click 모드 지원
* foreground / background 포인트 지원
* 전체 segmentation 시각화 지원
* 마스크 저장 기능 지원

### 2) 🧹 Inpainting-based Object Removal

사용자가 선택한 영역을 자연스럽게 지우고 싶을 때는 **Stable Diffusion Inpainting**을 사용합니다.
마스크 영역을 기준으로 주변 문맥을 참고해 자연스러운 배경을 생성하도록 구성했습니다.

### 3) 💬 Text-guided Editing

단순 제거뿐 아니라, 선택된 영역을 **텍스트 설명에 따라 다른 모습으로 바꾸는 편집 기능**도 제공합니다.

예를 들어,

* "빨간 꽃으로 바꿔줘"
* "모자를 쓰게 바꿔줘"
* "배경을 자연스럽게 채워줘"

와 같은 요청을 입력하면, 마스크 영역만 텍스트 조건에 맞춰 생성되도록 구성했습니다.

### 4) 🤖 GPT-based Prompt Refinement

사용자가 입력한 문장은 Stable Diffusion이 이해하기에 충분히 구체적이지 않을 수 있습니다.
그래서 GPT API를 이용해 사용자의 입력 문장을 **더 풍부하고 생성 친화적인 프롬프트**로 바꾼 뒤, 이를 이미지 생성 모델에 전달하도록 설계했습니다.

즉,

**사용자 문장 → GPT 보정 프롬프트 → Stable Diffusion 편집**

흐름으로 동작합니다.

---

## 🔄 4. System Pipeline

```text
User uploads image
        ↓
SAM-based mask generation
        ↓
Select editing mode
   ├── Remove / Inpainting
   └── Edit with text
                ↓
        User text input
                ↓
      GPT prompt refinement
                ↓
Stable Diffusion Inpainting
                ↓
     Edited image output
```

### 🪄 Two Main Modes

#### 🧹 Remove Mode

* 선택한 영역을 자연스럽게 제거
* 마스크와 원본 이미지를 Stable Diffusion Inpainting에 입력
* 주변 배경 문맥을 참고해 복원

#### ✏️ Edit Mode

* 사용자가 수정 문장을 입력
* GPT가 이를 더 적절한 이미지 생성 프롬프트로 변환
* 변환된 프롬프트와 마스크를 기반으로 Stable Diffusion Inpainting 수행

---

## 🛠️ 5. How It Was Implemented

### 🖥️ 5.1 Streamlit Web Demo

전체 데모는 Streamlit으로 구성했습니다.
복잡한 프론트엔드 프레임워크 없이도 다음 기능을 빠르게 연결할 수 있도록 설계했습니다.

* 이미지 업로드
* 사이드바 옵션 선택
* 클릭 인터랙션 처리
* 마스크 생성/저장
* Inpainting 및 편집 실행
* 결과 이미지 시각화

### 🎯 5.2 SAM-based Mask Generation

`app_save_mask.py`에서는 SAM을 불러와 사용자의 상호작용을 기반으로 마스크를 생성합니다.

주요 기능은 다음과 같습니다.

* `SamAutomaticMaskGenerator` 사용
* `SamPredictor` 사용
* foreground / background 포인트 입력 지원
* 클릭 좌표 누적 저장
* 마스크 시각화 및 저장

이 단계는 사용자가 직접 브러시로 영역을 칠하지 않아도 원하는 편집 영역을 잡을 수 있도록 해 줍니다.

### 🎨 5.3 Stable Diffusion Inpainting

`app_inpaint.py`에서는 `StableDiffusionInpaintPipeline`을 사용하여 선택 영역 편집을 수행합니다.

* `runwayml/stable-diffusion-inpainting` 모델 사용
* FP16으로 로드하여 GPU에서 실행
* 원본 이미지 + 마스크를 입력으로 사용
* 제거 모드에서는 간단한 기본 프롬프트 사용
* 편집 모드에서는 GPT 보정 프롬프트 사용

### 🤖 5.4 GPT Assistant Integration

편집 모드에서는 사용자가 입력한 문장을 그대로 쓰지 않고, OpenAI API를 통해 assistant를 불러와 프롬프트를 보정합니다.

이 과정을 통해,

* 짧고 모호한 사용자 입력을
* 더 구체적이고 생성 친화적인 문장으로 바꾸고
* 이를 Stable Diffusion에 전달

하도록 구성했습니다.

### 🧠 5.5 Why Prompt Refinement Matters

이미지 생성 모델은 프롬프트 품질에 따라 결과 차이가 크게 날 수 있습니다.
그래서 이 프로젝트는 사용자가 복잡한 prompt engineering을 하지 않아도 되도록, GPT를 중간 계층으로 두어 프롬프트 품질을 높이는 방향으로 설계했습니다.

---

## 🗂️ 6. Project Structure

```bash
.
├── app_save_mask.py
├── app_inpaint.py
├── images/
├── masks/
└── README.md
```

### 📄 File Description

* `app_save_mask.py`
  → Streamlit 기반 SAM 마스크 생성 앱

* `app_inpaint.py`
  → Streamlit 기반 Inpainting / Text-guided Editing 앱

---

## ⚙️ 7. Key Features

### 🎯 Interactive Segmentation

* 이미지 업로드
* Click / All segmentation 모드
* foreground / background point 입력
* 포인트 기반 mask predictor 실행
* 마스크 시각화 및 저장

### 🧹 Inpainting

* 선택 영역 제거
* Stable Diffusion Inpainting 적용
* 주변 문맥 기반 자연스러운 복원

### ✏️ Text-guided Editing

* 사용자가 텍스트 입력
* GPT API를 이용한 prompt 보정
* 보정된 프롬프트 기반 생성형 편집

### 🌐 Easy Web Demo

* Streamlit 기반 UI
* 빠른 프로토타이핑에 적합
* 실습/데모/교육용으로 활용 가능

---

## 🧩 8. Tech Stack

### 🖥️ Frontend / Demo

* Streamlit
* streamlit-drawable-canvas

### 🧠 AI Models

* Segment Anything Model (SAM)
* Stable Diffusion Inpainting
* GPT API / OpenAI Assistant

### 🧰 Libraries

* PyTorch
* diffusers
* OpenCV
* Pillow
* NumPy
* Matplotlib

---

## 🚀 9. Getting Started

### 1️⃣ Install Dependencies

```bash
pip install streamlit streamlit-drawable-canvas pillow numpy opencv-python matplotlib torch diffusers openai pandas
```

### 2️⃣ Prepare SAM Checkpoint

`app_save_mask.py`에서는 SAM 체크포인트 경로를 직접 지정하고 있습니다.
환경에 맞게 아래 경로를 수정해야 합니다.

```python
checkpoint="/home/baekw92/sam_vit_h_4b8939.pth"
```

### 3️⃣ Set OpenAI API Key

환경변수로 API 키를 설정합니다.

```bash
export OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

Windows PowerShell에서는 다음과 같이 설정할 수 있습니다.

```powershell
$env:OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

### 4️⃣ Run Segmentation App

```bash
streamlit run app_save_mask.py
```

### 5️⃣ Run Inpainting / Editing App

```bash
streamlit run app_inpaint.py
```

---

## 🧪 10. How to Use

### A. Generate Mask

1. 이미지를 업로드합니다.
2. Segmentation mode를 선택합니다.

   * `Click`
   * `All`
3. foreground / background 포인트를 지정합니다.
4. `generate mask` 버튼으로 마스크를 생성합니다.
5. 필요하면 `save mask` 버튼으로 마스크를 저장합니다.

### B. Remove Object

1. 원본 이미지와 마스크 이미지를 업로드합니다.
2. `Remove` 버튼을 클릭합니다.
3. 선택 영역이 자연스럽게 제거된 결과를 확인합니다.

### C. Edit with Text

1. 원본 이미지와 마스크를 업로드합니다.
2. 텍스트 입력창에 원하는 수정 내용을 작성합니다.
3. `generate image with text` 버튼을 클릭합니다.
4. GPT가 프롬프트를 보정한 뒤 Stable Diffusion이 편집 결과를 생성합니다.

---

## 🔍 11. What Makes This Project Meaningful?

이 프로젝트의 의미는 단순히 Stable Diffusion을 호출하는 데 있지 않습니다.
실제로는 이미지 편집을 구성하는 핵심 단계를 하나의 데모로 통합했다는 점이 중요합니다.

* **어디를 바꿀지**: SAM
* **무엇으로 바꿀지**: Stable Diffusion
* **어떻게 잘 설명할지**: GPT API
* **어떻게 쉽게 써볼지**: Streamlit

즉, 하나의 이미지 편집 경험을 위해 필요한 **영역 선택, 프롬프트 보정, 생성형 편집, 웹 데모화**를 모두 연결한 프로젝트입니다.

---

## ⚠️ 12. Limitations

* Stable Diffusion Inpainting 결과는 프롬프트와 마스크 품질에 크게 의존합니다.
* SAM 클릭 포인트가 부정확하면 편집 영역도 부정확해질 수 있습니다.
* 현재 구현은 빠른 프로토타입 중심이므로 예외 처리와 UI 완성도는 추가 개선 여지가 있습니다.
* OpenAI Assistant ID가 코드에 고정되어 있어, 환경에 따라 별도 assistant 구성이 필요할 수 있습니다.
* GPU 환경을 가정한 코드가 포함되어 있어 CPU-only 환경에서는 수정이 필요할 수 있습니다.

---

## 🔮 13. Future Work

* 🖌️ 브러시 기반 직접 마스크 편집 기능 추가
* 🧱 여러 편집 모드 지원 (replace / restyle / background edit)
* 🧠 LoRA 기반 스타일 커스터마이징
* 📱 더 직관적인 UI/UX 개선
* 🗂️ 편집 이력 저장 기능 추가
* 🆚 원본/결과 비교 뷰 추가
* 🌍 배포형 웹 서비스로 확장

---

## ✅ 14. Summary

이 프로젝트는 **SAM + Stable Diffusion + GPT API + Streamlit**을 결합해,

**사용자가 선택한 영역을 자연어 기반으로 편집할 수 있는 AI 이미지 편집기**

를 구현한 프로젝트입니다.

핵심은 단순 생성이 아니라,

**영역 선택 → 마스크 생성 → 프롬프트 보정 → 생성형 편집**

이라는 실제 이미지 편집 워크플로를 하나의 데모로 통합했다는 점입니다.

---

## 📎 15. Recommended README Add-ons

레포를 더 보기 좋게 만들고 싶다면 아래 항목도 추가하면 좋습니다.

* 🖼️ 전체 파이프라인 구조도
* 🎬 마스크 생성 / 편집 결과 GIF
* 📷 Before / After 예시 이미지
* 🙋 본인 담당 역할 정리
* 📚 관련 강의/실습 자료 출처
* 🧪 다양한 프롬프트 입력 예시

다음 단계에서는 원하면
**배지(badge) 추가 버전**, **영문 README 버전**, **스크린샷 배치 버전**으로 더 다듬을 수 있습니다.
