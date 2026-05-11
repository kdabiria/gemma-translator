# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single-file PWA (`index.html`) that translates text using Google Gemma models via the Hugging Face Inference API. No build step, no dependencies, no package manager.

## Live deployment

Hosted on GitHub Pages: **https://kdabiria.github.io/gemma-translator/**

Changes pushed to `master` deploy automatically via GitHub Pages.

## Running / testing locally

Open the file on a web server — the HF Inference API blocks requests from `file://` origins due to CORS. Quick options:

```bash
python3 -m http.server 8080
# or
npx serve .
```

Then open `http://localhost:8080`.

## Architecture

Everything lives in `index.html` as three inline sections:

- **`<style>`** — CSS custom properties (`--accent`, `--bg`, etc.) drive the dark theme. Mobile-first; side-by-side panels at ≥ 820px via CSS grid.
- **`<body>`** — Header (status pill + tweaks + settings), language bar, recent-lang chips, source/output panels, auto-translate toggle, sticky translate CTA, history section, tweaks floating panel, settings modal, toast.
- **`<script>`** — Vanilla JS, no frameworks. Key globals: `apiKey`, `model`, `maxTok`, `voicePrefs`, `history`, `recentLangs`, `tweaks`.

### API call flow

`runTranslation()` → `POST https://router.huggingface.co/featherless-ai/v1/chat/completions` with an OpenAI-compatible payload. Temperature fixed at 0.3. Supported models (configurable in Settings):

| Option | HF model ID |
|---|---|
| Gemma 3 12B (default) | `google/gemma-3-12b-it` |
| Gemma 3 27B | `google/gemma-3-27b-it` |

When source language is "Detect", the prompt asks the model to prefix output with `LANG: <name>` — stripped and shown in the detected-language pill.

### Voice input

Uses `webkitSpeechRecognition` / `SpeechRecognition`. On speech end, auto-submits if source text and API key are present.

### TTS voice picker

Chevron (▾) next to Speak button opens a per-language voice menu. `getBestVoice()` priority: saved pref → cloud voice (`localService === false`) → default → first match. Prefs stored in `voicePrefs` map, persisted to `localStorage`.

### Storage

| Key | Store | Contents |
|---|---|---|
| `gemma-key` | `sessionStorage` | API key — clears on tab close |
| `gemma-settings` | `localStorage` | model, maxTok, voicePrefs, autoTranslate, fromLang, toLang |
| `gemma-history` | `localStorage` | Last 30 translations |
| `gemma-recent-langs` | `localStorage` | Last 6 target languages |
| `gemma-tweaks` | `localStorage` | Feature toggles (showHistory, showChips, showDetect) |

### PWA

Inline service worker registered via a blob URL (workaround for same-origin restriction when hosted without a separate SW file). Provides basic offline fallback only.

## Key constraints

- **Must be hosted** — HF API requires a non-`file://` origin.
- The API key is stored in `sessionStorage` (`gemma-key`) only — clears on tab close, never goes to `localStorage`, never sent anywhere other than HF.
- Users must accept the Gemma model license on huggingface.co before their token will work (403 = license not accepted).
