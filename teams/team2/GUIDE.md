# 🧭 Team team2 — Configuration & Remote Access Guide

Welcome to your workspace! You’ll develop your chatbot prototype in an isolated environment.

---

## 📁 Folder & Workspace

Work exclusively in:
```
/srv/containers/team2
```
Each service (e.g., `api/`, `web/`) can live under this folder.

---

## 🗂️ Registry & Image Namespace

Your team’s namespace:
```
registry.<IP>.sslip.io/team2
```
Example (optional manual build/push):
```bash
podman build -t registry.<IP>.sslip.io/team2/app:latest .
podman push --tls-verify=false registry.<IP>.sslip.io/team2/app:latest
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
REGISTRY_NAMESPACE=team2
IMAGE_NAME=mentor-bot
TAG=latest
TEAM_SUBDOMAIN=team2
HOST_SUFFIX=<IP>.sslip.io
LITEMAAS_BASE_URL=https://lite-maas.example/api
LITEMAAS_API_KEY=<TEAM2_KEY>
PORT=8080
```

---

## 🚀 Build & Deploy

We recommend using `podman-compose` for a smooth workflow.

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
http://team2.<IP>.sslip.io
```

---

## 🌐 Remote Access (MiniCloud)

You do not need physical access to the server.

1) Provide your public SSH key to the hackathon host (for the `team2` user). The host will add it to `~team2/.ssh/authorized_keys`.

2) (Optional) Upload your key yourself if instructed:
```bash
ssh-copy-id -p 30022 -i ~/.ssh/id_ed25519.pub team2@<MINICLOUD_HOST>
```

3) SSH into the environment (SSH/SCP port is 30022):
```bash
ssh -p 30022 -i ~/.ssh/id_ed25519 team2@<MINICLOUD_HOST>
```

4) Navigate to your workspace and work as usual:
```bash
cd /srv/containers/team2
podman login $REGISTRY_HOST
podman-compose build && podman-compose up -d
podman-compose logs -f
```

5) Copy files with SCP (port 30022):
```bash
scp -P 30022 ./localfile team2@<MINICLOUD_HOST>:/srv/containers/team2/
```

6) Access your app via public route:
```
http://team2.<IP>.sslip.io
```

Optional (local port forward if needed):
```bash
ssh -p 30022 -i ~/.ssh/id_ed25519 -L 28080:127.0.0.1:8080 team2@<MINICLOUD_HOST>
# then open http://localhost:28080
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
