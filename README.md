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

### Sharing with users on the same WiFi / office network

`localhost` only works on the machine running the server. Others on the same network need your local IP:

```bash
# Linux / macOS — run this on your machine
hostname -I | awk '{print $1}'

# Windows
ipconfig | findstr "IPv4"
```

Send them the result, e.g. `http://192.168.1.42:7860` — they paste it into any browser.

---

### Sharing with anyone on the internet (home PC)

Your home router blocks outside traffic, so a local IP won't work for people elsewhere. Use a **tunnel** — it punches a hole through your router and gives you a public URL with no setup required.

#### Option A — ngrok (quick, link resets when you stop it)

1. Sign up free at [ngrok.com](https://ngrok.com) and install the app
2. Run:
   ```bash
   ngrok http 7860
   ```
3. ngrok prints a public URL like `https://abc123.ngrok-free.app` — share that link
4. The link works as long as the `ngrok` command is running

#### Option B — Cloudflare Tunnel (free, permanent link, no account limit)

1. Install [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/)
2. Run (no sign-in needed for a temporary link):
   ```bash
   cloudflared tunnel --url http://localhost:7860
   ```
3. Cloudflare prints a permanent-looking URL like `https://xxxx.trycloudflare.com` — share that
4. For a fixed custom URL every time, create a free Cloudflare account and follow their [named tunnel guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/)

Both options work on Windows, macOS, and Linux. No router changes, no firewall rules.

---

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
