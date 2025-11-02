# 🧭 Team team1 — Configuration & Remote Access Guide

Welcome to your workspace! You’ll develop your chatbot prototype in an isolated environment.

---

## 📁 Folder & Workspace

Work exclusively in:
```
/srv/containers/team1
```
Each service (e.g., `api/`, `web/`) can live under this folder.

---

## 🗂️ Registry & Image Namespace

Your team’s namespace:
```
registry.<IP>.sslip.io/team1
```
Example (optional manual build/push):
```bash
podman build -t registry.<IP>.sslip.io/team1/app:latest .
podman push --tls-verify=false registry.<IP>.sslip.io/team1/app:latest
```
Note: `podman-compose build` builds locally; pushing is optional unless your flow requires it.

---

## ⚙️ Environment Variables

Create your `.env` from the template and edit values:
```bash
cp ../../templates/container.env.template .env
```
Suggested keys to set:
```dotenv
REGISTRY_HOST=registry.<IP>.sslip.io
REGISTRY_NAMESPACE=team1
IMAGE_NAME=mentor-bot
TAG=latest
TEAM_SUBDOMAIN=team1
HOST_SUFFIX=<IP>.sslip.io
LITEMAAS_BASE_URL=https://lite-maas.example/api
LITEMAAS_API_KEY=<TEAM1_KEY>
PORT=8080
```

---

## 🚀 Build & Deploy

1) Copy the container template if needed:
```bash
cp ../../templates/Containerfile.template ./Containerfile
```
2) Login to the registry:
```bash
podman login $REGISTRY_HOST
```
3) Build & run:
```bash
podman-compose build
podman-compose up -d
```
4) Check your app via Traefik routing:
```
http://team1.<IP>.sslip.io
```

---

## 🌐 Remote Access (MiniCloud)

You do not need physical access to the server.

1) Obtain credentials from organizers:
- Host: `<MINICLOUD_HOST>` (e.g., `minicloud.<IP>.sslip.io` or a public IP)
- User: `team1`
- SSH key: your public key (recommended)

2) (If requested) Upload your SSH key:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub team1@<MINICLOUD_HOST>
```

3) SSH into the environment:
```bash
ssh -i ~/.ssh/id_ed25519 team1@<MINICLOUD_HOST>
```

4) Navigate to your workspace and work as usual:
```bash
cd /srv/containers/team1
podman login $REGISTRY_HOST
podman-compose build && podman-compose up -d
podman-compose logs -f
```

5) Access your app via public route:
```
http://team1.<IP>.sslip.io
```

Optional (local port forward if needed):
```bash
ssh -i ~/.ssh/id_ed25519 -L 18080:127.0.0.1:8080 team1@<MINICLOUD_HOST>
# then open http://localhost:18080
```

---

## 🧠 Quick Debugging Commands

| Action | Command |
|--------|---------|
| Check running | `podman ps` |
| See logs | `podman-compose logs -f` |
| Restart | `podman-compose up -d` |
| Stop | `podman-compose down` |
| Rebuild | `podman-compose build --no-cache` |

---

## ❤️ Red Hat Values Reminder
- Collaborate openly; share learnings
- Document decisions in your README
- Automate via scripts and Compose, avoid manual steps
- Respect peers; teamwork > competition

---

Happy hacking! If something breaks, focus on learning — the setup is designed to teach how containers and routing work together.
