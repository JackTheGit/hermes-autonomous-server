# 🚀 Hermes Agent – Autonomous Linux Server Setup

<p align="center">
  A clean, reboot-safe, headless deployment guide for Hermes Agent.
</p>

<p align="center">

![Linux](https://img.shields.io/badge/Linux-Tested-success?style=for-the-badge&logo=linux)
![Systemd](https://img.shields.io/badge/Systemd-Service-blue?style=for-the-badge)
![Headless](https://img.shields.io/badge/Headless-No%20Terminal-green?style=for-the-badge)
![Cron](https://img.shields.io/badge/Native-Cron%20Scheduler-purple?style=for-the-badge)
![Nous Portal Required](https://img.shields.io/badge/Nous%20Portal-Subscription-orange?style=for-the-badge)

</p>

Run **Hermes Agent** autonomously on your own dedicated Linux server using the native Hermes scheduler and systemd gateway service.

No shell loops.  
No fragile TTY hacks.  
No third-party hosting required.

Clean, production-ready architecture.

---

# 📌 Overview

This guide shows how to:

* Install Hermes Agent
* Authenticate with Nous Portal
* Create recurring AI cron jobs
* Run Hermes Gateway as a system service
* Ensure full headless operation
* Survive reboots automatically

Once configured, Hermes runs fully autonomous.

---

# 🧩 What This Is Not

- Not an official Hermes project
- Not affiliated with Nous Research
- Not a hosted service

This is a deployment guide for running Hermes autonomously on your own infrastructure.

---

# 🔑 Requirements

You need an active **Nous Portal subscription**:

👉 [https://portal.nousresearch.com/billing](https://portal.nousresearch.com/billing)

Hermes uses Nous Portal for model access.

Without an active subscription or API credits:

* Cron jobs will not execute
* Gateway will run but no tasks will fire

> 💡 At the time of writing, the basic subscription may be activated using the promotional code `HERMESAGENT` (availability subject to change).  
> Check the billing page for current eligibility.

---

# 🖥 Supported Environment

* Ubuntu / Debian (tested)
* Dedicated server or VPS
* systemd available
* Non-root user recommended

---

# 📦 Installation

## 1️⃣ Create a Dedicated User

```bash
sudo adduser hermes
sudo usermod -aG sudo hermes
su - hermes
```

---

## 2️⃣ Install Hermes Agent

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

Verify installation:

```bash
which hermes
```

Expected:

```
/home/hermes/.local/bin/hermes
```

---

## 3️⃣ Authenticate with Nous Portal

```bash
hermes login
```

Complete browser authentication.

Verify:

```bash
hermes status
```

You should see:

```
Nous Portal ✓ logged in
```

---

# ⏰ Create Autonomous Cron Job

Start Hermes:

```bash
hermes chat
```

Inside the interactive shell:

```
Create a cronjob that runs every 10 minutes.
The task should:
Write a short reflective paragraph about technology trends.
Limit response to 180 words.
```

Hermes will return a job ID.

Exit Hermes:

```
Ctrl + C
```

---

# ⚙ Enable Hermes Gateway (System-Level Service)

Some VPS environments do not support `systemctl --user`.
Use a system-level service instead.

Create service file:

```bash
sudo nano /etc/systemd/system/hermes-gateway.service
```

Paste:

```ini
[Unit]
Description=Hermes Gateway Service
After=network.target

[Service]
User=hermes
WorkingDirectory=/home/hermes
ExecStart=/home/hermes/.local/bin/hermes gateway
Restart=always
RestartSec=10
Environment=PATH=/home/hermes/.local/bin:/usr/local/bin:/usr/bin:/bin

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable hermes-gateway
sudo systemctl start hermes-gateway
```

---

# ✅ Verify Everything

Check gateway:

```bash
sudo systemctl status hermes-gateway
```

Check scheduler:

```bash
hermes cron status
```

Expected:

```
✓ Gateway is running — cron jobs will fire automatically
```

---

# 🏗 Architecture

```
Systemd (system service)
        ↓
Hermes Gateway
        ↓
Hermes Cron Scheduler
        ↓
Claude Model (Nous Portal)
        ↓
Local Output Files
```

---

# 💰 Cost Considerations

Example configuration:

* 10-minute interval
* ~180-word responses

Estimated usage:

~4,320 runs/month
Typically ~$8–12/month depending on model and output size.

To adjust frequency:

```
Update cronjob <job_id> to run every 15 minutes.
```

---

# 📊 Monitoring

Gateway logs:

```bash
journalctl -u hermes-gateway -f
```

Cron jobs:

```bash
hermes cron list
```

Gateway status:

```bash
hermes cron status
```

---

# 🔄 Reboot Safety

This setup:

* Auto-starts on reboot
* Does not require open SSH session
* Runs fully headless
* Automatically restarts if gateway crashes

---

# 🧼 Uninstall

```bash
sudo systemctl stop hermes-gateway
sudo systemctl disable hermes-gateway
hermes uninstall
```

---

# 🎯 Why This Approach?

✔ Uses native Hermes scheduler
✔ Avoids shell-based infinite loops
✔ Avoids TTY emulation hacks
✔ Clean systemd integration
✔ Minimal operational complexity
✔ Fully self-hosted

---

# ⚠ Important Notes

* Nous Portal subscription required:
  [https://portal.nousresearch.com/billing](https://portal.nousresearch.com/billing)
* Cron jobs require the gateway to be running
* API credits are consumed per execution
* Monitor billing regularly

---

# 🔒 Security Considerations

- Do not expose your server publicly without firewall rules.
- Avoid running Hermes as root.
- Monitor API usage regularly.
- Keep your server updated.

This guide assumes a trusted VPS or dedicated environment.

---

## Disclaimer

This repository is an independent deployment guide and is not affiliated with or endorsed by Nous Research.
