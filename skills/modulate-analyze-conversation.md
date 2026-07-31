---
name: Analyze a voice conversation with Velma-2
description: Upload a recorded audio file and get back a full conversation analysis — transcription, conversation type, participant roles, behaviors, topics, sentiment, and a summary — using Modulate Velma-2 batch, optionally seeded with behavior presets.
api: openapi/velma-2-batch-openapi-original.yml
operations:
  - velma2BatchListPresets
  - velma2Batch
---

# Analyze a voice conversation with Velma-2

Use the Modulate Velma-2 batch API to analyze a recorded conversation.

## Auth
Send your key in the `X-API-Key` header on every request. Base URL is `https://platform.modulate.ai`. Keys are created in the dashboard at `https://platform.modulate.ai/dashboard/api-keys`.

## Steps

1. **(Optional) Discover behavior presets** — call `velma2BatchListPresets`
   (`GET /api/velma-2-batch/list-presets`). Each returned `BehaviorPresetListing`
   has an `identifier` you can reference from a config as `preset:<identifier>`
   (e.g. `preset:complaints`).

2. **Run the analysis** — call `velma2Batch` (`POST /api/velma-2-batch`) as a
   `multipart/form-data` upload with the audio file plus a JSON `config`
   (`BatchConfig`) blob. In the config you may pass `conversation_types`,
   `participant_roles`, and `behaviors` (full `BehaviorDef` objects or
   `preset:<identifier>` strings), and toggle `produce_topics`,
   `produce_topic_sentiments`, and `produce_summary`. Omit `behaviors` to skip
   behavior evaluation.

3. **Read the result** — the `BatchResponse` returns `clips` (each with
   `clip_uuid`, text, timing, `speaker_label`, language, and optional emotion/
   accent/deepfake fields), `conversation_type_pick`, `participant_role_picks`,
   `behaviors` (`BehaviorDetection` with `detected`, `confidence`, and
   `evidence_clip_uuids` linking back to clips), `topics`, `topic_sentiments`,
   and a `summary`.

## Rules
- Supported audio: AAC, AIFF, FLAC, MOV, MP3, MP4, OGG, Opus, WAV, WebM. Batch max 100 MB; processing timeout is 60 seconds.
- On `401` the key is missing/invalid; on `403` model access is disabled or the monthly quota is exceeded; on `429` back off (exponential + jitter) — see `errors/modulate-error-codes.yml`.
- No idempotency key is supported; each call is an independent inference. See `conventions/modulate-conventions.yml`.
