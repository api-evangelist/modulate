---
name: Transcribe audio and redact PII/PHI
description: Transcribe a recorded audio file with Modulate, then remove sensitive content — either tag PII/PHI in the transcript during multilingual transcription, or fully redact it from both transcript text and audio with the redaction endpoint.
api: openapi/stt-batch-openapi-original.yml
operations:
  - transcribeMultilingualBatch
  - redactPiiPhiBatch
---

# Transcribe audio and redact PII/PHI

Two related flows for handling sensitive audio with Modulate. Base URL
`https://platform.modulate.ai`; authenticate with the `X-API-Key` header.

## Option A — transcribe with PII/PHI tagging
Call `transcribeMultilingualBatch` (`POST /api/velma-2-stt-batch`) with a
`multipart/form-data` audio upload. Enable optional enrichments via `STTOptions`:
`speaker_diarization` (default true), `emotion_signal`, `accent_signal`,
`deepfake_signal`, and `pii_phi_tagging`. With `pii_phi_tagging=true`, sensitive
spans are wrapped with tags in the transcript text but the original content is
preserved. Language is auto-detected per utterance (100 languages); pass the
optional `language` hint to constrain it.

## Option B — redact PII/PHI from transcript and audio
When the audio itself must be clean, call `redactPiiPhiBatch`
(`POST /api/velma-2-pii-phi-redaction-batch`). It replaces each detected PII/PHI
span with an empty marker tag (e.g. `<pii:name></pii:name>`, `<pii:ssn></pii:ssn>`,
`<phi></phi>`) in the transcript **and** silences the matching audio ranges in the
returned MP3.

## Rules
- Choose tagging (Option A) when downstream systems need to detect spans while keeping full text; choose redaction (Option B) when recordings will be shared, archived, or reviewed by parties who should not hear sensitive content.
- Supported audio and the 100 MB / 60-second batch limits apply. Errors follow `errors/modulate-error-codes.yml`; conventions in `conventions/modulate-conventions.yml`.
- By default no audio is stored; audio marked for storage is retained 35 days.
