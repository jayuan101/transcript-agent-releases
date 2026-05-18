# Transcript Agent

AI-powered transcription app for interviews and meetings. Automatically identifies speakers, generates formatted reports, and exports to Word or PDF. Works fully offline — your files never leave your machine.

---

## Download

Go to the [Releases](https://github.com/jayuan101/transcript-agent-releases/releases) page and grab the file for your platform:

| Platform | File | How to run |
|----------|------|------------|
| Windows 10/11 | `TranscriptAgent.exe` | Double-click to run |
| macOS | `TranscriptAgent-mac.zip` | Unzip, then double-click `TranscriptAgent` |
| Linux | `TranscriptAgent-linux.tar.gz` | Extract, then run: `chmod +x TranscriptAgent && ./TranscriptAgent` |

No installation, no Python, no Docker required on any platform.

---

## Features

### Transcription
- Powered by **OpenAI Whisper** — works on audio and video files
- **Auto-detects language** or set it manually
- Choose Whisper model size: `tiny` (fastest) → `large` (most accurate)

### Speaker Detection
- Enter the number of speakers, or leave it blank to **auto-detect**
- AI labels each speaker automatically (Speaker 1, Speaker 2, etc.)

### AI Analysis
- Generates a full formatted report: summary, key points, action items, speaker profiles, analytics
- Choose your report style: Formal, Casual, Executive, or Bullet Points
- Export to **Word (.docx)** or **PDF**

### AI Providers — Bring Your Own Key
Choose from any of these providers, or plug in your own:

| Provider | Notes |
|----------|-------|
| Claude (Anthropic) | Default — best for long documents |
| OpenAI (GPT-4o, o1) | |
| Google Gemini | |
| Groq | Fast inference |
| Mistral | |
| Together AI | |
| Perplexity | |
| Ollama (Local) | No API key needed — runs fully offline |
| **Custom (OpenAI-compatible)** | Any endpoint: LM Studio, vLLM, Azure OpenAI, etc. |

### Auto-Update
- The app checks for updates in the background every time it launches
- If a newer version is available, a banner appears with the **changelog**
- Click **Download & Install Update** — the app downloads, swaps itself, and restarts automatically
- No manual downloading needed

### Dynamic Progress & ETA
- Live progress bar with natural-language time remaining: *"2 minutes 30 seconds left"*, *"almost done!"*
- **"Done By"** shows the exact finish time in your local timezone (e.g. *"3:45 PM EST"*)
- Timezone auto-detected from your browser — or type any IANA zone (e.g. `America/Chicago`)

### Sleep Prevention
- Automatically keeps your computer awake during transcription so long jobs are never interrupted
- Restores normal sleep settings when done
- Works on Windows (Win32 API), macOS (`caffeinate`), and Linux (`systemd-inhibit`)

---

## Supported File Formats

**Audio:** `.mp3` `.wav` `.m4a` `.ogg` `.flac` `.aac` `.wma`  
**Video:** `.mp4` `.mkv` `.webm` `.mov` `.avi` `.m4v`

---

## System Requirements

| | Minimum | Recommended |
|--|---------|-------------|
| RAM | 8 GB | 16 GB |
| Disk | 5 GB free | 10 GB free |
| OS | Windows 10, macOS 12, Ubuntu 20.04 | Latest versions |

---

## How to Use

1. Launch the app — your browser opens automatically at `http://localhost:7860`
2. **Upload** a file or paste a file path / URL
3. Set your **AI provider** and paste your API key
4. Optionally set the **number of speakers** (or leave blank to auto-detect)
5. Click **Transcribe** and watch the live progress
6. Download your report as Word or PDF when done

---

## Updates

The app notifies you automatically. You can also check the [Releases](https://github.com/jayuan101/transcript-agent-releases/releases) page directly.
