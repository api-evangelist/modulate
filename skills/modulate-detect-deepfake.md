---
name: Detect deepfake (synthetic) voice
description: Analyze a recorded audio file for AI-generated (synthetic) speech with Modulate's Deepfake Detection batch model, returning per-frame deepfake scores and verdicts.
api: openapi/synthetic-voice-detection-batch-openapi-original.yml
operations:
  - detectSyntheticVoice
---

# Detect deepfake (synthetic) voice

Use Modulate's Deepfake Detection to flag AI-generated speech in recorded audio.
Base URL `https://platform.modulate.ai`; authenticate with the `X-API-Key` header.

## Steps
1. Call `detectSyntheticVoice` (`POST /api/velma-2-synthetic-voice-detection-batch`)
   with a `multipart/form-data` audio upload.
2. Read the per-frame results: each frame carries a `verdict` (e.g. `synthetic`,
   `human`, or `no-content`) and a `confidence` score from 0 to 1. A `no-content`
   frame is silent/unusable and always returns `confidence: 1.0`.

## Rules
- Audio must be at least 0.5 seconds or it is rejected with `422`; the recommended range is 4-60 seconds. Leading/trailing silence is trimmed automatically.
- For real-time detection, use the streaming companion (`asyncapi/modulate-synthetic-voice-detection-streaming-asyncapi.yml`), which requires headerless raw PCM (`s16le`, 16 kHz mono is the zero-cost passthrough) and query params `audio_format`, `sample_rate`, `num_channels`.
- Errors follow `errors/modulate-error-codes.yml`; back off on `429`/`503`.
