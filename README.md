# 🧱 Pi-hole Docker Stack with Optional ZFS Integration

This repository contains a minimal and production-ready [Pi-hole](https://pi-hole.net/) stack using Docker Compose. Pi-hole is a network-wide ad blocker that acts as a DNS sinkhole and optionally a DHCP server.

It supports optional ZFS integration for advanced users who want to mount Portainer data volumes onto a ZFS dataset, while remaining fully usable without ZFS.

---

## 📦 Features

- Official `pihole/pihole:latest` image
- Customizable ports via `.env`
- Volume persistence with optional **ZFS** dataset support
- DNS and optional DHCP services
- Optional HTTPS web UI
- Easy Git integration with `git_push.py`
- Preflight ZFS dataset validation (`preflight.sh`)
- Portable and Ansible-compatible

---

## 🚀 Getting Started

### 1. Clone the Repo

```bash
git clone https://github.com/Vantasin/Pihole.git
cd Pihole
```

### 2. Set Up Environment

Copy the example `.env` file and edit values:

```bash
cp env.example .env
nano .env
```

Customize variables such as:

- `PIHOLE_WEB_PORT=88`
- `PIHOLE_HTTPS_PORT=8443`
- `WEBPASSWORD=super-secret-password`
- `PIHOLE_DATA_VOLUME=/tank/docker/volumes/PiHole`
- `ZPOOL=tank`
- `PIHOLE_DATASET_PATH=docker/volumes/PiHole`
- `PIHOLE_DATASET=tank/docker/volumes/PiHole`

### 3. Optional: Prepare ZFS Dataset

If using ZFS, run the preflight script:

```bash
./preflight.sh
```

This will validate your ZFS pool and dataset path, creating it if it doesn’t exist.

---

## 🐳 Deploy with Docker Compose
---

## 🐳 Deployment Options

### 🅰️ Option A: Run with ZFS

If you are using a ZFS-backed volume:

1. Ensure your `.env` file defines ZFS variables like:
   ```ini
   ZPOOL=tank
   PIHOLE_DATASET_PATH=docker/volumes/PiHole
   PIHOLE_DATASET=tank/docker/volumes/PiHole
   PIHOLE_DATA_VOLUME=/tank/docker/volumes/PiHole
   ```

2. Run the ZFS preflight script to verify or create the dataset:
   ```bash
   ./preflight.sh
   ```

3. Launch the container:
   ```bash
   docker compose up -d
   ```

---

### 🅱️ Option B: Run without ZFS

If you're not using ZFS, simply define the `PIHOLE_DATA_VOLUME` path in `.env`, for example:
```ini
PIHOLE_DATA_VOLUME=./data
```

Then bring up the stack:
```bash
docker compose up -d
```

No preflight is needed.

---

## 🌐 Accessing Pi-hole Web UI

Once deployed, access Pi-hole using:

- **Web Interface (HTTP):** `http://localhost:88/admin` or replace `localhost` with your server’s IP address.  
- **Admin Password:** Must be set via `WEBPASSWORD` in `.env` or a random password will be generated.

> **Note:** Consider using [Nginx Proxy Manager](https://github.com/Vantasin/Nginx-Proxy-Manager.git) as a reverse proxy for HTTPS certificates via Let's Encrypt.

## ⚙️ Optional: Ansible Integration

Use this snippet in your Ansible playbook to template the `.env` file:

```yaml
- name: Template .env for {{ stack.name }}
  template:
    src: "{{ compose_root }}/{{ stack.name }}/env.j2"
    dest: "{{ compose_root }}/{{ stack.name }}/.env"
```

Where `compose_root` is `/tank/docker/compose`.

This allows you to inject variables from Ansible inventory or vault.

---

## 📄 File Overview

| File                 | Description                                           |
|----------------------|-------------------------------------------------------|
| `docker-compose.yml` | Pi-hole container definition                        |
| `env.example`        | Example environment file for local overrides          |
| `env.j2`             | Ansible template for generating `.env`                |
| `preflight.sh`       | Optional script to prepare ZFS dataset (if enabled)   |
| `git_push.py`        | Helper script to stage, commit, and push to all remotes |
| `summarize_codebase.sh` | Script to generate codebase summary                 |

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 🙏 Acknowledgments

- [pi-hole/docker-pi-hole](https://github.com/pi-hole/docker-pi-hole)
- [Pi-hole documentation](https://docs.pi-hole.net/)
- [Docker](https://www.docker.com/)
- [ZFS on Linux](https://openzfs.org/)
