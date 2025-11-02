# 🧭 Team ${TEAM} — Configuration Guide

Welcome to your workspace!  
You’ll be developing your chatbot prototype in your own isolated environment.

---

## 📁 Folder & Workspace

You will work exclusively in:
```
/srv/containers/${TEAM}
```

Each service (e.g., `api/`, `web/`) lives under this folder.

---

## 🗂️ Registry & Image Namespace

Your team’s private namespace in the registry is:

```
registry.<IP>.sslip.io/${TEAM}
```

Example:
```bash
podman build -t registry.<IP>.sslip.io/${TEAM}/api:latest ./api
podman push --tls-verify=false registry.<IP>.sslip.io/${TEAM}/api:latest
```

---

## ⚙️ Environment Variables

Copy and edit your `.env` file:
```bash
cp .env.example .env
```

You’ll need:
```dotenv
TEAM=${TEAM}
HOST_WEB_PORT=${PORT}
LITEMAAS_BASE_URL=https://litellm-litemaas.apps.prod.rhoai.rh-aiservices-bu.com
LITEMAAS_API_KEY=<TEAM_SPECIFIC_KEY>
LITEMAAS_MODEL=Llama-4-Scout-17B-16E-W4A16
```

---

## 🚀 Build & Deploy

We recommend using `podman-compose`.

1. Build your containers:
   ```bash
   podman build -t registry.<IP>.sslip.io/${TEAM}/web:latest ./web
   podman build -t registry.<IP>.sslip.io/${TEAM}/api:latest ./api
   ```

2. Push to registry (optional if not required):
   ```bash
   podman push --tls-verify=false registry.<IP>.sslip.io/${TEAM}/web:latest
   podman push --tls-verify=false registry.<IP>.sslip.io/${TEAM}/api:latest
   ```

3. Launch your app:
   ```bash
   podman-compose up -d
   ```

4. Check your app:
   ```
   http://${TEAM}.<IP>.sslip.io
   ```

---

## 🌐 Remote Access (MiniCloud)

- Provide your public SSH key to the host; it will be added to `~${TEAM}/.ssh/authorized_keys`.
- SSH/SCP port: `30022`
- SSH: `ssh -p 30022 ${TEAM}@<MINICLOUD_HOST>`
- SCP: `scp -P 30022 ./localfile ${TEAM}@<MINICLOUD_HOST>:/srv/containers/${TEAM}/`
- Workspace on server: `/srv/containers/${TEAM}`
- Use `podman-compose` for build/run on the server

---

## 🧠 Quick Debugging Commands

| Action | Command |
|--------|----------|
| Check running containers | `podman ps` |
| See logs | `podman-compose logs` |
| Restart | `podman-compose restart` |
| Stop | `podman-compose down` |
| Rebuild | `podman-compose build --no-cache` |

---

## ❤️ Red Hat Values Reminder

- **Collaborate openly** – share your learnings.  
- **Document decisions** – your README is part of your code.  
- **Automate** – use scripts and Compose files, not manual steps.  
- **Respect peers** – teamwork > competition.

---

Happy hacking! 🚀  
If something breaks, focus on learning — the setup is designed to teach *how* containers and routing work together.
