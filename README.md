# Transcript Agent

AI-powered transcription and interview analysis app for meetings and interviews. Auto-identifies speakers, scores interview responses, generates formatted reports, and exports to PDF, Word, and more. Runs entirely on your machine — your files never leave your device.

---

## Download — Desktop App

| Platform | File | How to run |
|----------|------|------------|
| Windows 10/11 | `TranscriptAgent.exe` | Double-click to run |
| macOS | `TranscriptAgent.dmg` | Open DMG → drag to Applications → double-click |

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

---

## How to Use

1. Launch the app — browser opens automatically at `http://localhost:7860`
2. **Upload** a file or paste a file path / URL
3. Choose your **Transcription Engine** (Whisper local or a cloud provider)
4. Choose your **AI provider** and paste your API key
5. Optionally set the **number of speakers** (leave blank to auto-detect)
6. Click **Transcribe** and watch the live progress
7. Download your report in any format

---

## Features

### Transcription
- **9 STT engines** — Whisper (local/offline), Deepgram, AssemblyAI, Groq Whisper, OpenAI Whisper API, Google Cloud STT, Azure Speech, ElevenLabs Scribe, Rev.ai
- **STT timing** — shows exactly how long the transcription step took per engine
- Auto-detects language or set it manually
- Choose Whisper model size: `tiny` (fastest) → `large` (most accurate)
- **Fast startup** — app UI loads instantly; Whisper model loads in the background

### Speaker Detection
- Enter the number of speakers, or leave blank to **auto-detect**
- AI labels each speaker automatically

### Interview Mode
- **Question extraction** — identifies every question the interviewer asked
- **Answer scoring** — rates each response: Great / Good / Needs Improvement / Missed
- **Ideal answers** — shows how you could have answered each question
- **Coaching tips** — specific, actionable feedback per question
- **Deep mode** — deflection detection, % likelihood of advancing, prep guide for weak questions

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
| Custom (OpenAI-compatible) | LM Studio, vLLM, Azure, any compatible endpoint |

### Downloads

Every job produces all of these:

| Format | Description |
|--------|-------------|
| `.pdf` | Formatted PDF report |
| `.docx` | Word document |
| `.md` | Markdown report |
| `.txt` | Plain text transcript |
| `.json` | Structured data |
| `.srt` | Subtitles for video players |
| `.vtt` | WebVTT subtitles |

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

---

## Supported Formats

**Audio:** `.mp3` `.wav` `.m4a` `.ogg` `.flac` `.aac` `.wma`  
**Video:** `.mp4` `.mkv` `.webm` `.mov` `.avi` `.m4v`  
**Documents:** `.pdf` `.docx` `.txt` `.md` `.srt` `.vtt`

---

## System Requirements

| | Minimum | Recommended |
|--|---------|-------------|
| RAM | 8 GB | 16 GB |
| Disk | 5 GB free | 10 GB free |
| OS | Windows 10, macOS 12 | Latest |
