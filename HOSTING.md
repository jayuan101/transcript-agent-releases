# Transcript Agent — Hosting & Server Setup

This document covers how to run Transcript Agent as a shared server so multiple users can access it from their browsers. No installation required on their end.

---

## Run with Docker

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

```bash
# Update to latest version
docker pull sushi0934/transcript-agent:latest
docker stop transcript-agent && docker rm transcript-agent
# then re-run the docker run command above

# Stop
docker stop transcript-agent && docker rm transcript-agent
```

`GRADIO_QUEUE_MAX_SIZE` — max requests waiting in queue  
`GRADIO_DEFAULT_CONCURRENCY_LIMIT` — max jobs running at the same time

---

## How users connect

### Same WiFi / office network

Find your local IP (run on the server machine):

```bash
# Linux / macOS
hostname -I | awk '{print $1}'

# Windows
ipconfig | findstr "IPv4"
```

Send users: `http://<that-ip>:7860`

### Over the internet from a home PC

Your router blocks outside traffic — use a tunnel instead.

**ngrok** (quick, link resets when stopped):
```bash
ngrok http 7860
```
Share the URL it prints, e.g. `https://abc123.ngrok-free.app`

**Cloudflare Tunnel** (free, more stable):
```bash
cloudflared tunnel --url http://localhost:7860
```
Share the URL it prints, e.g. `https://xxxx.trycloudflare.com`

---

## Free hosting on Oracle Cloud (recommended)

**4 vCPU · 24 GB RAM · $0/month — permanently free**

### Step 1 — Create an Oracle Cloud account

1. Go to [cloud.oracle.com](https://cloud.oracle.com) → **Start for free**
2. Fill in name, email, choose a **Home Region** closest to you (can't change later)
3. Enter address and credit card (identity check only — you won't be charged)
4. Log in to the OCI Console

---

### Step 2 — Create the VM

1. Menu (top left) → **Compute** → **Instances** → **Create instance**
2. Name it `transcript-agent`
3. Click **Edit** under Image and shape:
   - **Change image** → Ubuntu → **Ubuntu 22.04** → Select image
   - **Change shape** → Ampere → **VM.Standard.A1.Flex**
   - Set **OCPUs: 4**, **Memory: 24 GB**
   - Select shape
4. Under **Add SSH keys** → **Generate a key pair for me** → **Save private key**
   Keep that file — you need it to log in
5. Click **Create** and wait ~2 min for status to show **Running**

---

### Step 3 — Open port 7860

**In Oracle's firewall:**
1. Click your instance → **Primary VNIC** → click the **Subnet** link
2. Click the **Security list** → **Add ingress rules**
3. Fill in:
   - Source CIDR: `0.0.0.0/0`
   - Protocol: `TCP`
   - Destination port: `7860`
4. Save

**In the OS firewall** (run after connecting in Step 4):
```bash
sudo iptables -I INPUT -p tcp --dport 7860 -j ACCEPT
sudo netfilter-persistent save
```

---

### Step 4 — Connect via SSH

**Mac / Linux:**
```bash
chmod 400 /path/to/your-key.key
ssh -i /path/to/your-key.key ubuntu@<your-instance-public-ip>
```

**Windows (Windows Terminal):**
```bash
ssh -i C:\path\to\your-key.key ubuntu@<your-instance-public-ip>
```

Type `yes` when asked to confirm the connection. Your prompt becomes `ubuntu@transcript-agent:~$`

Find your public IP: OCI Console → Compute → Instances → click your instance → **Public IP address**

---

### Step 5 — Install Docker

Paste these one at a time and wait for each to finish:

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

Verify:
```bash
docker --version
```

---

### Step 6 — Run the app

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

First run downloads the image (a few minutes). Check it's running:
```bash
docker ps
```

---

### Step 7 — Share the URL

```
http://<your-instance-public-ip>:7860
```

Share this with anyone. It works from anywhere in the world, no tunnel needed.

---

## What users need

Users need nothing installed. They open the URL in any browser and:

1. Upload an audio or video file
2. Choose an AI provider and paste their own API key
3. Click Transcribe

Each user brings their own API key — it is not stored on the server. The server handles all transcription (Whisper runs locally on the VM).
