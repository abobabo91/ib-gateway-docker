# IB Gateway Docker - Setup Guide

This folder contains a Dockerized Interactive Brokers (IB) Gateway, optimized for automated trading bots. It includes a built-in virtual desktop for GUI access and robust 2FA handling.

## 🚀 Quick Start (Server Deployment)

1. **Pull the Code:**
   ```bash
   git clone <YOUR_REPO_URL>
   cd ib-gateway-docker
   ```

2. **Initialize Secrets:**
   Create a `.env` file (this is ignored by Git for security):
   ```bash
   nano .env
   ```
   Paste the following content (update passwords as needed):
   ```bash
   TWS_USERID=kovacsikbot
   TWS_PASSWORD=R-Ny(TVu7*n?X*Abot
   VNC_SERVER_PASSWORD=secret123
   TIME_ZONE=Europe/Budapest
   TRADING_MODE=live
   TWOFA_TIMEOUT=180
   TWOFA_TIMEOUT_ACTION=restart
   TWS_ACCEPT_INCOMING=accept
   EXISTING_SESSION_DETECTED_ACTION=primary
   BYPASS_WARNING=yes
   ```

3. **Start the Container:**
   ```bash
   docker compose up -d
   ```

---

## 🛡️ Secure GUI Access (SSH Tunneling)
For security, the GUI is restricted to `localhost`. To access it from your computer, you must create an SSH tunnel to your server's **Reserved IP** (`159.223.245.52`).

### Option 1: Web Browser (Recommended)
1. **On your LOCAL computer**, run:
   ```bash
   ssh -L 6080:127.0.0.1:6080 root@159.223.245.52
   ```
2. **Open your browser** and go to: `http://localhost:6080`
3. **Password:** `secret123`

### Option 2: VNC Client
1. **On your LOCAL computer**, run:
   ```bash
   ssh -L 5900:127.0.0.1:5900 root@159.223.245.52
   ```
2. **Connect your VNC viewer** to: `127.0.0.1:5900`
3. **Password:** `secret123`

---

## 🤖 Connection Details for Bots
*   **API Port:** `4003` (Use this in your trading bot settings if running on the server)
*   **Internal Port:** `4001` (If the bot is in the same Docker network)

---

## 🛠️ Useful Commands
*   **View Logs:** `docker compose logs -f` (Crucial for seeing 2FA requests)
*   **Restart:** `docker compose restart`
*   **Stop:** `docker compose down`

---

## 🛡️ 2FA & Stability
*   **2FA Window:** You have **3 minutes (180s)** to approve the 2FA notification on your phone.
*   **Auto-Recovery:** If 2FA fails or times out, the container will automatically restart and try again.
*   **Daily Restart:** Scheduled for **11:59 PM** to maintain connection health.
*   **Frequency:** You should only need to 2FA when the container first starts or during weekly IBKR maintenance (usually Sundays).
