# 🚀 100% Free ClassQuiz Self-Hosting Guide

This guide walks you through setting up and running your own **ClassQuiz** server completely **for free** using **Docker Compose** + **Cloudflare Tunnels** (giving you a free public HTTPS URL without needing to pay for a VPS, open router ports, or buy a domain).

---

## 📋 Architecture Overview

When you run ClassQuiz via Docker Compose, it launches:
- **`proxy` (Caddy)**: Handles reverse proxy routing to frontend and API on port `8000`.
- **`frontend` (SvelteKit)**: Serves the interactive user interface and player arena.
- **`api` (FastAPI / Python)**: Handles quiz management, user accounts, and real-time Socket.IO communication.
- **`worker` (Arq)**: Background task execution.
- **`db` (PostgreSQL 14)**: Persistent relational database.
- **`redis` (Valkey)**: Fast in-memory state and real-time gameplay synchronization.
- **`meilisearch`**: Blazing-fast quiz search engine.

---

## 🛠️ Step 1: Prerequisites

Make sure you have Docker installed on your system:
- **Windows**: Install [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/) (ensure WSL 2 backend is enabled).
- **Linux / Mac**: Install [Docker Engine & Docker Compose](https://docs.docker.com/engine/install/).

Verify Docker is working by running in PowerShell or Terminal:
```powershell
docker --version
docker compose version
```

---

## ⚙️ Step 2: Configure Environment

1. Inside the `ClassQuiz` directory, copy `.env.example` to `.env`:
   ```powershell
   Copy-Item .env.example .env
   # Or on Linux/macOS:
   # cp .env.example .env
   ```

2. Generate a secure secret key:
   - Run in PowerShell:
     ```powershell
     -join ((65..90) + (97..122) + (48..57) | Get-Random -Count 32 | ForEach-Object {[char]$_})
     ```
   - Or on Linux / macOS:
     ```bash
     openssl rand -hex 32
     ```

3. Open `.env` in a text editor and set:
   ```ini
   SECRET_KEY=your_generated_secret_key_here
   ROOT_ADDRESS=http://localhost:8000
   SKIP_EMAIL_VERIFICATION=True
   ```

---

## 🐳 Step 3: Launch ClassQuiz Locally

Run the following command in the project root:

```powershell
docker compose up -d
```

Docker will download the images, initialize the PostgreSQL database, Redis, Meilisearch, and start the frontend + backend services.

### Test Local Access:
Open your browser and navigate to:
👉 **[http://localhost:8000](http://localhost:8000)**

You should now see the ClassQuiz home page!

---

## 🌐 Step 4: Expose to the Internet for FREE (Cloudflare Tunnel)

To let friends, students, or colleagues join your quizzes from anywhere without exposing your home IP or paying for cloud hosting:

### Quick Method: Instant Free Public URL (No Signup Required)
1. Download `cloudflared` (Cloudflare's lightweight tunnel tool):
   - **Windows (PowerShell)**:
     ```powershell
     winget install --id Cloudflare.cloudflared
     ```
   - **macOS**: `brew install cloudflare/cloudflare/cloudflared`
   - **Linux**: `sudo apt install cloudflared`

2. Run an instant temporary tunnel pointing to ClassQuiz:
   ```powershell
   cloudflared tunnel --url http://localhost:8000
   ```

3. Cloudflare will output a free temporary HTTPS URL (e.g., `https://random-words.trycloudflare.com`).
4. Update `ROOT_ADDRESS` in `.env` (or in `docker-compose.yml`) to match this URL and restart:
   ```powershell
   docker compose restart
   ```

---

### Permanent Method: Free Custom Domain with Cloudflare Zero Trust (Recommended)

1. Get a free domain (or use an existing domain) and add it to a free [Cloudflare](https://dash.cloudflare.com/) account.
2. In Cloudflare Dashboard, go to **Zero Trust** > **Networks** > **Tunnels**.
3. Click **Create a Tunnel** (name it `classquiz`).
4. Select your operating system and run the install command provided by Cloudflare.
5. In the **Public Hostnames** tab of your tunnel:
   - Subdomain: `quiz` (or whatever you choose)
   - Domain: `yourdomain.com`
   - Service Type: `HTTP`
   - URL: `localhost:8000`
   - In Additional Application Settings > HTTP Settings, enable **WebSockets** and **No TLS Verify**.
6. Set `ROOT_ADDRESS=https://quiz.yourdomain.com` in `.env`.
7. Enjoy a 100% free, 24/7 HTTPS-secured quiz platform with DDoS protection!

---

## 🎮 Step 5: Managing Your Server

### Stopping the Server:
```powershell
docker compose down
```

### Viewing Logs:
```powershell
docker compose logs -f
```

### Updating to Latest Images:
```powershell
docker compose pull
docker compose up -d
```

### Backing up Data:
All database data is safely stored in the `data` Docker volume, and uploaded images are in `./uploads/`.

---

## 💡 Troubleshooting & Tips

- **Socket.IO / WebSocket issues**: Ensure your proxy / tunnel supports WebSockets (Cloudflare tunnels have WebSocket support enabled by default).
- **Port already in use**: If port `8000` is used by another program, change `"8000:8080"` under `proxy.ports` in `docker-compose.yml` to `"8080:8080"` or any free port.
- **Registrations**: If you want to disable public registrations after creating your admin account, set `REGISTRATION_DISABLED=True` in the backend environment.
