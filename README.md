# Gemma Translate

A mobile-first PWA for translating text using Google Gemma models via the Hugging Face Inference API.

**Live:** https://kdabiria.github.io/gemma-translator/

## Features

- Translate between 16 languages
- Voice input (Web Speech API)
- Text-to-speech output
- Language swap with content carry-over
- Offline-capable (service worker)

## Models

| Model | Provider |
|---|---|
| Gemma 3 12B | featherless-ai via HF Router |
| Gemma 3 27B | featherless-ai via HF Router |

## Setup

1. Get a free token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens) (read access sufficient)
2. Accept the Gemma license at [huggingface.co/google/gemma-3-12b-it](https://huggingface.co/google/gemma-3-12b-it)
3. Open the app, go to Settings, paste your token

> **Note:** Must be hosted — HF API blocks `file://` requests. Use `python3 -m http.server` or deploy to GitHub Pages.

## Local dev

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

No build step. Single `index.html` file.
