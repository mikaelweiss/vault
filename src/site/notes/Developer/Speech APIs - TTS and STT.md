---
{"dg-publish":true,"permalink":"/developer/speech-ap-is-tts-and-stt/","dg-note-properties":{}}
---


Notes from evaluating text-to-speech and speech-to-text APIs, mostly in the context of building [[Timbre\|Timbre]].

---

## My Current Stack (as of 2026-05)

- **TTS:** xAI Grok — cheaper than ElevenLabs, good voices, simple pay-per-use API
- **STT:** ElevenLabs (streaming via WebSocket) — best transcription accuracy, rarely need to edit output. Falls back to non-streaming REST if the WebSocket drops (bad internet)

---

## Services Evaluated

### ElevenLabs

**TTS**
- Great voice quality, very natural
- Downside: requires a subscription to use the API (no pure pay-per-token option)
- Pricing (per 1K characters): Flash/Turbo $0.05, Multilingual v2/v3 $0.10 — but character allowance is tied to subscription tier ($6–$990/mo)

**STT (Scribe)**
- Best transcription quality I've found — almost never have to go back and edit
- WebSocket streaming API works great, very fast results
- Pricing: Scribe v1/v2 $0.22/hr, Scribe v2 Realtime $0.39/hr — hours included scale with subscription tier
- Extra features: entity detection ($0.07/hr), keyterm prompting ($0.05/hr)

### xAI (Grok)

**TTS**
- Good voice quality, natural sounding
- Simple pay-per-use pricing: $4.20 / 1M characters
- No subscription required — I prefer this model over ElevenLabs' subscription
- Streaming and batch output, multiple formats (MP3, WAV, PCM, μ-law, A-law)

**STT**
- REST: $0.10/hr, Streaming: $0.20/hr
- Accuracy noticeably worse than ElevenLabs — had to go back and edit frequently
- Streaming API uses Deepgram spec, which was confusing to work with. Claude Code got confused by it too, and I couldn't get it to be reliable — randomly dropped the beginning or end of sentences

### OpenAI (GPT)

**STT**
- Transcription quality on par with ElevenLabs — rarely need to edit
- No streaming API though, which is a dealbreaker for Timbre where I need instant results when I stop talking

### Groq

- Good developer experience — clean docs, "copy for LLM" buttons
- STT streaming uses Deepgram spec — same issues I had with xAI (unreliable, dropped sentences)
- Haven't used extensively

### Deepgram

- Haven't used their API directly yet
- Their WebSocket spec (used by xAI and Groq for streaming STT) was frustrating to work with
- Transcription quality seems okay but not as good as ElevenLabs
- Undecided overall

### Fish Audio

- Cheap TTS
- Bad privacy policy — they retain all your data and use it for training
- Ruled out for that reason

### Amazon (Polly)

- TTS voices didn't sound natural

### Google Cloud TTS

- Voices sounded really natural
- But Google Cloud APIs are confusing to work with — would rather use a simpler API like xAI

---

## Lessons Learned

- **Pricing research is painful.** Every provider structures it differently (per character, per hour, subscription tiers, included allowances). Hard to do apples-to-apples comparisons.
- **Streaming matters for latency.** For Timbre, I need results the instant I stop talking — WebSocket streaming (ElevenLabs) lets me get partial results live instead of waiting for a full response.
- **WebSocket fallback is important.** On bad internet, WebSockets drop. My Timbre implementation falls back to non-streaming REST when the ElevenLabs WebSocket connection drops.
- **Cheap STT + LLM cleanup is a pattern.** Some transcription apps use a cheaper/lower-quality STT model and then run a fast LLM pass to fix formatting and errors. Haven't tried this yet — worried about the extra API call hurting Timbre's speed.
- **Deepgram's WebSocket spec is hard to work with.** Both xAI and Groq use it for streaming STT and I had reliability issues with both — sentences getting dropped at the start or end.
- **Pay-per-use > subscriptions.** I prefer APIs where I just pay for what I use (xAI) over requiring a monthly subscription (ElevenLabs). ElevenLabs STT quality is good enough to justify it for now, though.

---

## STT Deep Dive (2026-05 Research)

### Global Accuracy Rankings (Artificial Analysis AA-WER v2.0 benchmark)

| Model | AA-WER | Cost/Hr (Batch) | Cost/Hr (Streaming) |
|-------|--------|-----------------|---------------------|
| ElevenLabs Scribe v2 | 2.3% | $0.22 | $0.39 |
| Google Gemini 3 Pro | 2.9% | — | — |
| Mistral Voxtral Small | 3.0% | — | — |
| OpenAI GPT-4o Transcribe | ~5-6% (8.9% on AA) | $0.36 | N/A (no streaming) |
| Deepgram Nova-3 | ~6.8% (12.8% on AA) | $0.26 | $0.46 |
| Google Chirp 3 (V2 API) | ~10-15% | $0.96 | $0.96 |
| Google Chirp 3 (24hr Batch) | ~10-15% | $0.24 | N/A |

**ElevenLabs Scribe v2 is #1 in the world on the most respected independent benchmark.** Also leads on voice-agent speech (1.6% WER) and earnings calls (4.1% WER).

### Why ElevenLabs Is So Good

- Almost certainly uses an LLM-augmented decoder (Conformer encoder + LLM decoder) — explains why it handles technical jargon well
- Massive proprietary training data (likely 500k+ hours)
- Predictive punctuation — anticipates next word/punctuation before spoken
- Streaming latency: ~150ms p50, ~250ms p95, ~400ms p99

### Streaming vs Batch (ElevenLabs)

Streaming wins past ~1-2 seconds of audio. Batch = recording_duration + upload + processing. Streaming = ~150ms after you stop talking (processing happens in parallel). Keep the WebSocket open the whole session.

### Best Local/Open-Source STT Models

**Accuracy leaders:**
- NVIDIA Canary-Qwen 2.5B — 5.63% WER (#1 on HuggingFace Open ASR Leaderboard). FastConformer encoder + Qwen3-1.7B LLM decoder. ~8GB VRAM. Batch only, not natively streaming.
- IBM Granite Speech 3.3 8B — ~5.85% WER, Apache 2.0. Heavy (8B params).
- Qwen3-ASR (Alibaba, Jan 2026) — 52 languages, 1.7B/0.6B sizes. Ecosystem still maturing.
- Whisper Large V3 — huge ecosystem but hallucination problem (phantom text during silences), not natively streaming (30s chunks).

**Speed/streaming leaders:**
- NVIDIA Parakeet TDT 1.1B — RTFx >2,000 (6.5x faster than Canary-Qwen). RNN-Transducer enables streaming. ~2GB VRAM. Best for WebSocket-style local streaming.
- Moonshine v2 — designed for edge/on-device, runs on laptop CPU.
- faster-whisper + WhisperLive — pseudo-streaming with ~1-2s latency, 3-6GB VRAM.

**No single local model matches ElevenLabs on both speed and accuracy.**

### The "Cheap STT → LLM Cleanup" Pipeline (Verdict: Won't Work For Me)

- Fundamental problem for technical dictation: if the STT mangles framework names or paths, an LLM is guessing — and guessing wrong on code is worse than showing me the error
- Latency math: STT step alone ~300ms + LLM round-trip ~200ms+ = 400-700ms minimum. Can't hit sub-100ms.
- ElevenLabs is fast because it's one hop. Two-model chain is always slower.
- Might work for general English but not for dev jargon dictation.

### Apple Intelligence as Post-Processor (Verdict: Possible But Slow)

- Foundation Models framework ships with macOS 26 — gives Swift apps direct access to the ~3B on-device model. Free inference.
- `apple-intelligence-cli` wraps it in TypeScript with OpenAI-compatible localhost API.
- Speed: ~30 tokens/sec on iPhone 15 Pro (faster on Mac). Time-to-first-token ~0.6ms per prompt token.
- For a correction task (~20-40 output tokens): 300-1300ms generation time. Not sub-100ms.
- Worth experimenting with for zero-cost offline pipeline, but won't match ElevenLabs latency.

### What It Would Take to Build My Own ElevenLabs-Quality Model

- **Data:** Need 500k+ hours labeled speech. Public datasets combined = ~100k hours (order of magnitude short).
- **Compute:** NVIDIA Canary-Qwen trained 150k steps on 128 A100s. Ballpark: $500K-$2M+ in GPU compute.
- **Architecture:** Hybrid speech-encoder + LLM-decoder (FastConformer + small LLM).
- **Distillation shortcut:** Run audio through ElevenLabs API → use transcriptions as pseudo-labels → train smaller student model. Cost: $2k-$40k in API calls + GPU training. Check ToS first.
- **Realistic path for solo dev:** Fine-tune Canary-Qwen or Parakeet on your own domain audio. Won't match 2.3% WER globally but can close the gap for your specific vocabulary.

### Deepgram On-Prem vs Self-Hosted Open Models

Deepgram's value isn't accuracy (Nova-3 scores worse than open models on independent benchmarks). It's operational: containerized K8s/Docker deployment, auto-scaling, streaming out of the box, enterprise support/SLAs. If you have ML engineering capacity and don't need streaming at scale in regulated environments, self-hosting open models is increasingly viable — especially for batch.

---

## User Usage Estimates — STT & TTS (2026-05 Research)

No single published study tracks per-user monthly consumption broken down by segment — that data lives inside Otter, ElevenLabs, Speechify, etc. These estimates are triangulated from **app pricing tiers** (companies calibrate free/pro/business caps to actual usage distributions), **reported professional usage patterns**, and **platform limit structures**.

### Speech-to-Text — Hours of Transcription per Month

| Segment | Hours/Month | Who this is |
|---------|------------:|-------------|
| Light | 3–5 | Occasional meetings, voice notes, a few lectures |
| Normal | 15–20 | Daily dictation or meetings (4–5/week), moderate content creation |
| Heavy | 60–100 | Full-time dictation writers, journalists, medical/legal professionals, agencies |

**Key evidence:**
- **Otter.ai tiers:** Free 300 min (5 hrs) → Pro 1,200 min (20 hrs) → Business 6,000 min (100 hrs)
- **Notta tiers:** Free 120 min (2 hrs) → Pro 1,800 min (30 hrs) → Business unlimited
- **Descript tiers:** Free 1 hr → Creator 10 hrs → Pro 30 hrs
- **Reported professional usage:** 4–5 meetings/week = 10–15 hrs/mo transcription
- **Content producers:** Regular 10–20 hrs/mo, heavy 30+ hrs/mo
- **Sonix industry stat:** 62% of professionals using automated transcription save 4+ hrs/week

### Text-to-Speech — Words per Month

| Segment | Words/Month | Audio Equivalent | Who this is |
|---------|------------:|-----------------:|-------------|
| Light | 5K–15K | 30–100 min | Occasional voiceovers, trying out TTS, short read-alouds |
| Normal | 50K–150K | 5–17 hrs | Regular content creator, student read-aloud, daily article listening |
| Heavy | 300K–1M | 33–110 hrs | Audiobook narration, e-learning platforms, heavy accessibility use |

**Key evidence:**
- **ElevenLabs tiers:** Free 10K chars (~1.5K words) → Starter 30K (~4.5K words) → Creator 100K (~15K words) → Pro 500K chars (~83K words) → Scale 2M → Business 11M
- **Speechify:** Paid baseline 150K words/mo, 2026 guaranteed limit 1M words/mo
- **NaturalReader free:** 20 min/day of premium voices ≈ ~10 hrs/mo ≈ ~110K words
- **Conversion:** ~150 words/min average speaking rate → 10K words ≈ 67 min of audio

**Important distinction:** Consumption TTS (Speechify, NaturalReader — reading articles/books aloud) drives much higher word counts than production TTS (ElevenLabs — generating voiceovers). A Speechify user passively listening to articles all day uses 10–50x more words than a creator generating a 5-minute narration.

### API Cost per User (at Timbre's current rates)

Using ElevenLabs Scribe v2 Realtime at **$0.39/hr** for STT and xAI Grok at **$4.20/1M characters** for TTS (~6 chars/word):

| Segment | STT Cost | TTS Cost | Combined |
|---------|----------:|----------:|---------:|
| Light (4 hrs, 10K words) | $1.56 | $0.25 | **~$1.81** |
| Normal (17 hrs, 100K words) | $6.63 | $2.52 | **~$9.15** |
| Heavy (80 hrs, 650K words) | $31.20 | $16.38 | **~$47.58** |

TTS is dramatically cheaper than STT per user. Even a heavy TTS user costs less than a normal STT user. This is why most consumer apps gate their free tiers on transcription minutes, not TTS output.

### Suggested Product Tier Caps

If building pricing tiers, set caps with headroom above the midpoint of each segment:

| Tier | STT Cap | TTS Cap |
|------|--------:|--------:|
| Free | 5 hrs | 50K words |
| Pro | 25 hrs | 200K words |
| Business | 100 hrs | 1M words |
