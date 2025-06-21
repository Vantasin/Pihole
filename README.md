# 📦 Pi-hole Docker Compose Stack

[![MIT License](https://img.shields.io/github/license/Vantasin/WatchTower?style=flat-square)](LICENSE)
[![Woodpecker CI](https://img.shields.io/badge/Woodpecker%20CI-self--hosted-green?logo=drone&style=flat-square)](https://woodpecker-ci.org/)
[![Docker Pulls: pihole/pihole](https://img.shields.io/docker/pulls/pihole/pihole?style=flat-square&logo=docker)](https://hub.docker.com/r/pihole/pihole)
[![envsubst](https://img.shields.io/badge/envsubst-variable%20substitution-lightgrey?style=flat-square)](https://man7.org/linux/man-pages/man1/envsubst.1.html)
[![ZFS](https://img.shields.io/badge/ZFS-OpenZFS-blue?style=flat-square)](https://openzfs.org/)

This repository contains a minimal and production-ready [Pi-hole](https://pi-hole.net/) stack using Docker Compose. Pi-hole is a network-wide ad blocker that acts as a DNS sinkhole and optionally a DHCP server.

---

## 📁 Directory Structure

```bash
tank/
├── docker/
│   ├── compose/
│   │   └── Pihole/              # Git repo lives here
│   │       ├── docker-compose.yml  # Main Docker Compose config
│   │       ├── .env                # Runtime environment variables and secrets (gitignored!)
│   │       ├── env.example         # Example .env file for reference
│   │       ├── env.template        # Optional template
│   │       ├── .woodpecker.yml     # CI/CD pipeline definition for auto-deploy
│   │       └── README.md           # This file
│   └── data/
│       └── Pihole/              # Volume mounts and persistent data
```

---

## 🧰 Prerequisites

* Docker Engine
* Docker Compose V2
* Git
* (Optional) ZFS on Linux for dataset management

> ⚠️ **Note:** These instructions assume your ZFS pool is named `tank`. If your pool has a different name (e.g., `rpool`, `zdata`, etc.), replace `tank` in all paths and commands with your actual pool name.

---

## ⚙️ Setup Instructions

1. **Create the stack directory and clone the repository**

   If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/compose/Pihole
   cd /tank/docker/compose/Pihole
   sudo git clone https://github.com/Vantasin/Pihole.git .
   ```

   If using standard directories:
   ```bash
   mkdir -p ~/docker/compose/Pihole
   cd ~/docker/compose/Pihole
   git clone https://github.com/Vantasin/Pihole.git .
   ```

2. **Create the runtime data directory** (optional)

   If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/data/Pihole
   ```

   If using standard directories:
   ```bash
   mkdir -p ~/docker/data/Pihole
   ```

3. **Configure environment variables**

   Copy and modify the `.env` file:

   ```bash
   sudo cp env.example .env
   sudo nano .env
   sudo chmod 600 .env
   ```

   > Alternatively generate the `.env` file using the `env.template` template with Woodpecker CI's `.woodpecker.yml`.

4. **Start Pihole**

   ```bash
   docker compose up -d
   ```

---

## 🌐 Accessing Pi-hole Web UI

Once deployed, access Pi-hole using:

- **Web Interface (HTTP):** `http://localhost:88/admin` or replace `localhost` with your server’s IP address.  
- **Admin Password:** Must be set via `WEBPASSWORD` in `.env` or a random password will be generated.

> **Note:** Consider using [Nginx Proxy Manager](https://github.com/Vantasin/Nginx-Proxy-Manager.git) as a reverse proxy for HTTPS certificates via Let's Encrypt.

---

## 🚀 Continuous Deployment with Woodpecker

This project includes a `.woodpecker.yml` pipeline for automated deployment using [Woodpecker CI](https://woodpecker-ci.org/).

When changes are pushed to the Git repository:
1. The pipeline is triggered by the Woodpecker server.
2. Secrets are securely injected from the Woodpecker UI.
3. The `.env` file is rendered from `env.template` using `envsubst`.
4. The Docker Compose stack is restarted to apply updates.

### 🔐 Secret Injection

Secrets must be added in the Woodpecker **Web UI > Repositories > Pihole > Secrets** section with the following names:

| Secret Name                | Description                       |
|----------------------------|-----------------------------------|
| `WEBPASSWORD`              | FTLconf Web-server API password   |

They are injected automatically into the pipeline environment as secure variables.

---

## 🙏 Acknowledgments

- [ChatGPT](https://openai.com/chatgpt) for assistance in generating setup scripts and templates.
- [pi-hole/docker-pi-hole](https://github.com/pi-hole/docker-pi-hole) the official Docker image and source code for Pi-hole.
- [Pi-hole documentation](https://docs.pi-hole.net/) the official user guide and admin reference for configuring and troubleshooting Pi-hole.
- [Docker](https://www.docker.com/) for container orchestration and runtime.
- [`envsubst`](https://man7.org/linux/man-pages/man1/envsubst.1.html) for lightweight environment variable substitution in template files.
- [Woodpecker CI](https://woodpecker-ci.org/) for lightweight, self-hosted continuous integration.
- [ZFS](https://openzfs.org/) for advanced local filesystem features, dataset organization, and snapshotting.
