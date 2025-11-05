# ☁️ Build Your Own Mini Cloud on macOS (Apple Silicon)
**Podman + Traefik + Private Registry Setup Guide (Tested on Apple M4 Pro)**

> This guide explains how to replicate the hackathon “mini cloud” setup on your own Mac.  
> It runs a local container environment using **Podman**, **Traefik**, and a **private container registry**, exactly like the one used during the event.

---

## 🧰 Overview

Your mini cloud will provide:
- A **Podman runtime** for containers and pods  
- A **local registry** to store and share images  
- A **Traefik reverse proxy** for host-based routing  
- SSL-free (development) setup with `.sslip.io` hostnames  
- Rootless, secure environment running entirely on macOS  

**Architecture:**

```
           +-------------------------------+
           |           macOS Host          |
           |                               |
           |  +-----------+   +---------+  |
           |  | Registry  |←→ | Podman  |  |
           |  +-----------+   +---------+  |
           |        ↑               ↑      |
           |        │               │      |
           |        ▼               ▼      |
           |     Traefik ←→ Multiple pods  |
           |                               |
           +-------------------------------+
```

Each service (registry, apps, etc.) runs as a **Podman container**.  
`Traefik` dynamically routes HTTP traffic based on hostnames like:

```
http://registry.<your-ip>.sslip.io
http://team1.<your-ip>.sslip.io
```

---

## 🪜 Step 1 — Prerequisites

### ✅ macOS Requirements
- macOS **Sonoma or later** (Apple M1, M2, M3, or M4)
- At least **16 GB RAM** (recommended: 32–48 GB)
- Admin access for installation

### 🧩 Required Software

| Tool | Purpose | Install Command |
|------|----------|----------------|
| **Homebrew** | macOS package manager | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| **Podman** | Container engine (rootless) | `brew install podman` |
| **Podman Compose** | Compose file support | `brew install podman-compose` |
| **Traefik** | Reverse proxy and router | `brew install traefik` |
| **jq / netcat / curl** | Helpers for validation | `brew install jq netcat curl` |

---

## ⚙️ Step 2 — Initialize Podman Machine

On macOS, Podman runs in a lightweight virtual machine.

```bash
podman machine init --cpus 8 --memory 8192 --disk-size 40
podman machine start
```

Validate:
```bash
podman info
```

If it shows a running VM, you’re good to go.

> 💡 You can adjust resources as needed:  
> `--memory 8192` = 8GB RAM inside the VM.  
> Increase this if you run multiple pods.

---

## 🧱 Step 3 — Directory Layout

Use this structure on your Mac:

```
/srv/containers/
├── traefik/
│   ├── traefik.yaml
│   └── rules/
│       └── registry.yaml
└── registry/
    └── data/
```

Create the folders:
```bash
sudo mkdir -p /srv/containers/{registry/data,traefik/rules}
sudo chown -R $USER /srv/containers
```

---

## 🐳 Step 4 — Run the Private Registry

Create and run your registry as a **rootless container**:

```bash
podman run -d --name registry   --network=host   -v /srv/containers/registry/data:/var/lib/registry:Z   -e REGISTRY_HTTP_ADDR=0.0.0.0:5000   -e REGISTRY_STORAGE_DELETE_ENABLED=true   registry:2
```

Verify locally:
```bash
curl http://127.0.0.1:5000/v2/_catalog
# Expect: {"repositories":[]}
```

---

## 🌐 Step 5 — Install & Configure Traefik

### 1. Create the configuration file:

`/srv/containers/traefik/traefik.yaml`
```yaml
entryPoints:
  web:
    address: ":80"

providers:
  file:
    directory: /srv/containers/traefik/rules
    watch: true

api:
  dashboard: true
```

### 2. Create the registry routing rule

`/srv/containers/traefik/rules/registry.yaml`
```yaml
http:
  routers:
    registry:
      rule: Host(`registry.<YOUR-IP>.sslip.io`) && PathPrefix(`/v2/`)
      entryPoints: [web]
      service: registry-service
  services:
    registry-service:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:5000"
```

> Replace `<YOUR-IP>` with your LAN IP, e.g. `192.168.1.117`.

### 3. Run Traefik

```bash
podman run -d --name traefik   --network=host   -v /srv/containers/traefik/traefik.yaml:/etc/traefik/traefik.yaml:Z   -v /srv/containers/traefik/rules:/etc/traefik/rules:Z   traefik:v3.0
```

Check logs:
```bash
podman logs -f traefik
```

### 4. Verify Traefik

Visit:
```
http://127.0.0.1:8080/dashboard/
```

If you see the dashboard, routing is active.

---

## 🧩 Step 6 — Test the Registry via Traefik

```bash
curl http://registry.<YOUR-IP>.sslip.io/v2/_catalog
# Expected output: {"repositories":[]}
```

You now have:
✅ Traefik  
✅ Registry  
✅ Host networking  
✅ Working `.sslip.io` resolution

---

## 🧮 Step 7 — Push & Pull Test

Login:
```bash
podman login registry.<YOUR-IP>.sslip.io --tls-verify=false
```

Tag and push a test image:
```bash
podman pull quay.io/podman/hello
podman tag quay.io/podman/hello registry.<YOUR-IP>.sslip.io/test/hello:latest
podman push --tls-verify=false registry.<YOUR-IP>.sslip.io/test/hello:latest
```

List in catalog:
```bash
curl http://registry.<YOUR-IP>.sslip.io/v2/_catalog
```

---

## 🧠 Step 8 — Multi-Team Setup (Optional)

For hackathons or group work, create subfolders:

```
/srv/containers/
├── team1/
├── team2/
├── team3/
```

Each team has:
- Its own `compose.yaml`
- Its own `.env`
- Unique hostname in Traefik (e.g. `team1.<IP>.sslip.io`)

You can route additional services by adding more rule files under `/srv/containers/traefik/rules/`.

Example team rule:
```yaml
http:
  routers:
    team1:
      rule: Host(`team1.<YOUR-IP>.sslip.io`)
      entryPoints: [web]
      service: team1-service
  services:
    team1-service:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:18081"
```

---

## 🔄 Step 9 — Update IP Script (Dynamic Networks)

If your Mac changes network (e.g., new IP on Wi-Fi), use this script to update Traefik rules automatically.

Create `/usr/local/bin/update-traefik-ip.sh`:

```bash
#!/bin/bash
set -e

CONFIG_DIR="/srv/containers/traefik/rules"
NEW_IP=$(ipconfig getifaddr en0)

echo "Updating Traefik rules with IP: $NEW_IP"

find "$CONFIG_DIR" -type f -name "*.yaml" -exec   sed -i '' -E "s/[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/$NEW_IP/g" {} \;

podman restart traefik
echo "✅ Traefik IP updated to $NEW_IP"
```

Make it executable:
```bash
chmod +x /usr/local/bin/update-traefik-ip.sh
```

Run after reconnecting to any new network:
```bash
update-traefik-ip.sh
```

---

## 🧾 Step 10 — Maintenance Commands

| Task | Command |
|------|----------|
| View running containers | `podman ps` |
| Stop a container | `podman stop <name>` |
| Remove a container | `podman rm <name>` |
| Restart all services | `podman restart registry traefik` |
| Check logs | `podman logs -f <name>` |
| Update Traefik IP | `update-traefik-ip.sh` |

---

## 🧑‍🏫 Learning Outcomes

By completing this setup, you’ll understand:
- How **rootless containers** work on macOS
- How to **host your own registry**
- How **Traefik** routes HTTP traffic dynamically
- How to use **sslip.io** for DNS-less local testing
- How to maintain a reproducible **“mini cloud”** environment

---

## 🧩 Troubleshooting

| Symptom | Likely Cause | Fix |
|----------|---------------|-----|
| Registry unreachable | Wrong IP in Traefik rule | Run `update-traefik-ip.sh` |
| `curl` works but Podman pull fails | Forgot `--tls-verify=false` | Add the flag |
| Port 80 in use | macOS AirPlay or another service | Disable AirPlay Receiver in System Settings |
| `podman-compose` missing | Not installed via Homebrew | `brew install podman-compose` |
| Slow network startup | Podman machine under-resourced | `podman machine stop` → `podman machine rm` → reinit with more memory |

---

## 🧾 Summary

| Component | Role | Port |
|------------|------|------|
| Registry | Stores container images | 5000 |
| Traefik | Routes requests to containers | 80 |
| Dashboard | Traefik admin interface | 8080 |

---

## 🏁 You Now Have Your Own Mini Cloud!

You’ve set up:
- ✅ Podman-based container runtime  
- ✅ Private image registry  
- ✅ Dynamic hostname routing with Traefik  
- ✅ Rootless, local development cloud on macOS  

You can now use this as a **learning lab** or **mini Red Hat-style container platform** to run hackathons, demos, or your own projects.
