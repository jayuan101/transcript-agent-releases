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

## Host for Free on Oracle Cloud (24 GB RAM, always free)

Oracle gives you a permanently free server — 4 CPU cores and 24 GB RAM — enough to run Transcript Agent for multiple users with no monthly bill. Here's how to set it up from scratch.

---

### Step 1 — Create an Oracle Cloud account

1. Go to [cloud.oracle.com](https://cloud.oracle.com) and click **Start for free**
2. Fill in your name, email, and choose a **Home Region** (pick the one closest to you — you can't change it later)
3. Enter your address and a credit card — Oracle uses it for identity verification only, you won't be charged
4. Complete sign-up and log in to the **OCI Console**

---

### Step 2 — Create your free server (VM)

1. In the OCI Console, click the **≡ menu** (top left) → **Compute** → **Instances**
2. Click **Create instance**
3. Give it a name, e.g. `transcript-agent`
4. Under **Image and shape**, click **Edit**:
   - Click **Change image** → select **Ubuntu** → pick **Ubuntu 22.04** → click **Select image**
   - Click **Change shape** → select **Ampere** → choose **VM.Standard.A1.Flex**
   - Set **OCPUs: 4** and **Memory: 24 GB** (this is within the free tier)
   - Click **Select shape**
5. Under **Add SSH keys** — select **Generate a key pair for me**, then click **Save private key**. Save that file somewhere safe — you'll need it to log in
6. Leave everything else as default and click **Create**

Wait about 2 minutes for the instance to show **Running** in green.

---

### Step 3 — Open port 7860 (firewall)

Oracle's firewall blocks all ports by default. You need to open port 7860.

1. Click on your instance name to open its details page
2. Under **Instance information**, find **Primary VNIC** and click the **Subnet** link
3. On the Subnet page, click the **Security list** link
4. Click **Add ingress rules** and fill in:
   - **Source CIDR:** `0.0.0.0/0`
   - **IP Protocol:** `TCP`
   - **Destination port range:** `7860`
5. Click **Add ingress rules** to save

Now do the same for the OS firewall — you'll run this after connecting in Step 4:

```bash
sudo iptables -I INPUT -p tcp --dport 7860 -j ACCEPT
sudo netfilter-persistent save
```

---

### Step 4 — Connect to your server

You'll control the server by typing commands into a terminal (SSH).

**On Mac / Linux:**

1. Open Terminal
2. Find the private key file you downloaded in Step 2 (likely `ssh-key-YYYY-MM-DD.key`)
3. Run:
   ```bash
   chmod 400 /path/to/your-key.key
   ssh -i /path/to/your-key.key ubuntu@<your-instance-public-ip>
   ```
   Replace `<your-instance-public-ip>` with the IP shown on your instance page in OCI Console

**On Windows:**

1. Download [PuTTY](https://www.putty.org/) or use Windows Terminal with OpenSSH
2. In Windows Terminal:
   ```bash
   ssh -i C:\path\to\your-key.key ubuntu@<your-instance-public-ip>
   ```

When asked `Are you sure you want to continue connecting?` type `yes` and press Enter.

You're now inside your server. The prompt will look like `ubuntu@transcript-agent:~$`

---

### Step 5 — Install Docker

Run these commands one by one (copy, paste, press Enter, wait for each to finish):

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker ubuntu
newgrp docker
```

Check it worked:
```bash
docker --version
```

You should see something like `Docker version 26.x.x`

---

### Step 6 — Run Transcript Agent

Now run the same Docker command as usual — the app will start automatically every time the server boots:

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

The first run downloads the image (~a few minutes). Check it's running:

```bash
docker ps
```

You should see `transcript-agent` listed with status `Up`.

---

### Step 7 — Open the app

In your browser, go to:

```
http://<your-instance-public-ip>:7860
```

Use the same public IP from Step 4. Share this URL with anyone — it works from anywhere in the world, for free, with no tunnel required.

**To find your public IP any time:** log into [cloud.oracle.com](https://cloud.oracle.com) → Compute → Instances → click your instance → look for **Public IP address**.

---

### Keeping it updated

To pull the latest version of Transcript Agent:

```bash
docker pull sushi0934/transcript-agent:latest
docker stop transcript-agent && docker rm transcript-agent
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
