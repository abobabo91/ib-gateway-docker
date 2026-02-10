# IB Gateway Docker - Setup Guide

This folder contains a Dockerized Interactive Brokers (IB) Gateway, optimized for automated trading bots. It includes a built-in virtual desktop for GUI access and robust 2FA handling.

## 📁 Volume Management (Important)

This setup uses persistent volumes to ensure that your logs, settings, and session data are preserved across container restarts. This is critical for reducing the number of 2FA requests and for debugging.

### Persistence Overview
- **`./logs`**: Contains IB Gateway and IBC log files.
- **`./tws_settings`**: Stores your TWS/Gateway configuration.
- **`./jts.ini`**: The main IBKR initialization file.
- **`./config.ini`**: The IBC configuration file.

### Accessing Logs
To view the logs of a running or killed process:
1.  Check the `./logs` directory on your host machine.
2.  Use `tail -f logs/FILENAME.log` to watch the logs in real-time.

---

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
   TWS_PASSWORD=asdasd
   VNC_SERVER_PASSWORD=secret123
   TIME_ZONE=Europe/Budapest
   TRADING_MODE=live
   TWOFA_TIMEOUT=1800
   TWOFA_TIMEOUT_ACTION=restart
   TWOFA_EXIT_INTERVAL=60
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
*   **View Logs:** `docker compose logs -f` (or check the `./logs` folder)
*   **Restart:** `docker compose restart`
*   **Stop:** `docker compose stop` (keeps container state)
*   **Remove:** `docker compose down` (safe to use with volumes)

---

## 🛡️ 2FA & Stability
*   **2FA Window:** You have **30 minutes (1800s)** to approve the 2FA notification on your phone.
*   **Auto-Recovery:** If 2FA fails or times out, the container will restart and try again.
*   **Daily Restart:** Scheduled for **11:59 PM** to maintain connection health.
*   **Frequency:** With volumes enabled, you should only need to 2FA when the container first starts or during weekly IBKR maintenance.
