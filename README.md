# Transcript Agent

AI-powered transcription app for interviews and meetings. Automatically identifies speakers, generates formatted reports, and exports to Word or PDF. Works fully offline — your files never leave your machine.

---

## Download — Desktop App

| Platform | File | How to run |
|----------|------|------------|
| Windows 10/11 | `TranscriptAgent.exe` | Double-click to run |
| macOS | `TranscriptAgent.dmg` | Open DMG → drag to Applications → double-click |
| Linux | `TranscriptAgent-linux.AppImage` | `chmod +x TranscriptAgent-linux.AppImage` then double-click |

**→ [Go to Releases](https://github.com/jayuan101/transcript-agent-releases/releases)**

No installation, no Python, no Docker required. App opens in its own window.

---

## Run with Docker

Prefer Docker? One command to pull and run:

```bash
docker run -d \
  --name transcript-agent \
  -p 7860:7860 \
  -v transcript-agent-outputs:/app/outputs \
  -v transcript-agent-cache:/app/.cache \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_QUEUE_MAX_SIZE=20 \
  -e GRADIO_DEFAULT_CONCURRENCY_LIMIT=5 \
  --restart unless-stopped \
  sushi0934/transcript-agent:latest
```

Then open **http://localhost:7860** in your browser.

```bash
# Pull latest update
docker pull sushi0934/transcript-agent:latest

# Stop
docker stop transcript-agent && docker rm transcript-agent
```

### Sharing with other users

`localhost` only works on the machine running the server. Other users need your machine's **actual IP address**.

**Step 1 — Find your IP (run this on the server machine):**

```bash
# Linux / macOS
hostname -I | awk '{print $1}'

# Windows
ipconfig | findstr "IPv4"
```

Example output: `192.168.1.42`

**Step 2 — Send that address to your users.** They open this URL in any browser:

```
http://192.168.1.42:7860
```

Replace `192.168.1.42` with whatever your command printed.

> **Same network required.** This works for people on the same WiFi or office network. If users are on the internet (different location), you'd need to expose the port through your router or use a tunnel like [ngrok](https://ngrok.com) — run `ngrok http 7860` and share the URL it gives you.

Multiple users are supported via the built-in request queue — new requests wait their turn rather than failing. Adjust `GRADIO_QUEUE_MAX_SIZE` and `GRADIO_DEFAULT_CONCURRENCY_LIMIT` in the `docker run` command to suit your needs.

---

## How to Use

1. Launch the app — browser opens automatically at `http://localhost:7860`
2. **Upload** a file or paste a file path / URL
3. Choose your **AI provider** and paste your API key
4. Optionally set the **number of speakers** (leave blank to auto-detect)
5. Click **Transcribe** and watch the live progress
6. Download your report as Word or PDF

---

## Features

### Transcription
- Powered by **OpenAI Whisper** — works on any audio or video file
- Auto-detects language or set it manually
- Choose model size: `tiny` (fastest) → `large` (most accurate)

### Speaker Detection
- Enter the number of speakers, or leave blank to **auto-detect**
- AI labels each speaker automatically

### AI Providers — Bring Your Own Key
| Provider | Notes |
|----------|-------|
| Claude (Anthropic) | Default |
| OpenAI (GPT-4o, o1) | |
| Google Gemini | |
| Groq | Fast inference |
| Mistral | |
| Together AI | |
| Perplexity | |
| Ollama (Local) | No API key — fully offline |
| **Custom (OpenAI-compatible)** | LM Studio, vLLM, Azure, any compatible endpoint |

### Auto-Update
- App checks for updates in the background on every launch
- Banner shows the changelog and a one-click **Download & Install** button
- Swaps itself and restarts automatically — no manual download needed

### Dynamic ETA
- Live progress: *"2 minutes 30 seconds left"*, *"almost done!"*
- **Done By** shows exact finish time in your local timezone
- Timezone auto-detected from browser — or override with any IANA zone

### Sleep Prevention
- Keeps your machine awake during transcription
- Restores normal sleep when done
- Works on Windows, macOS, and Linux

---

## Supported Formats

**Audio:** `.mp3` `.wav` `.m4a` `.ogg` `.flac` `.aac` `.wma`  
**Video:** `.mp4` `.mkv` `.webm` `.mov` `.avi` `.m4v`

---

## System Requirements

| | Minimum | Recommended |
|--|---------|-------------|
| RAM | 8 GB | 16 GB |
| Disk | 5 GB free | 10 GB free |
| OS | Windows 10, macOS 12, Ubuntu 20.04 | Latest |
