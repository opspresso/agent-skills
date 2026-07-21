---
name: image-generation
description: >
  사용자의 아이디어를 이미지 생성 모델이 잘 이해하는 영어 프롬프트로 다듬어요.
  주제·스타일·구도·조명·품질 키워드 순서로 구성하고 negative prompt를 분리해요.
---

# 이미지 생성 프롬프트 스킬

사용자의 아이디어를 이미지 생성 모델이 잘 이해하는 프롬프트로 다듬는 지침이에요.
직접 이미지를 만드는 대신, 바로 사용할 수 있는 완성된 프롬프트를 산출물로 제공해요.

## 프롬프트 구성 요소
좋은 이미지 프롬프트는 다음 순서로 구성해요:
1. **주제** — 무엇을 그릴지 한 문장으로 명확하게 (인물, 사물, 장면)
2. **스타일** — photorealistic, watercolor, flat illustration, 3D render, pixel art 등
3. **구도** — close-up, wide shot, bird's eye view, rule of thirds 등
4. **조명과 분위기** — golden hour, soft studio lighting, neon, moody 등
5. **품질 키워드** — highly detailed, sharp focus, 8k 등 (2~3개면 충분해요)

## 작성 규칙
- 최종 프롬프트는 **영어**로 작성해요 (대부분의 이미지 모델이 영어에 최적화).
- 쉼표로 구분된 구문 나열보다 자연스러운 문장 1~2개 + 스타일 키워드가 좋아요.
- 피해야 할 요소가 있으면 negative prompt를 별도로 정리해요.
- 사람 얼굴, 브랜드 로고, 실존 인물 묘사 요청은 정책에 어긋날 수 있음을 안내해요.

## 산출물 형식
```
Prompt: <영어 프롬프트>
Negative prompt: <선택, 영어>
권장 비율: <1:1 | 16:9 | 9:16 등>
```
프롬프트 아래에 어떤 선택을 왜 했는지 한두 문장으로 설명해요.
