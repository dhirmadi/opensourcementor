# Podman + Registry + Compose — Quick Cheatsheet

Use this as a fast reference during the hackathon. For deeper guidance, see `docs/QUICKSTART_PODMAN.md` and `docs/TROUBLESHOOTING.md`.

## Basics
- Version: `podman --version`
- Help: `podman <command> --help`
- Current containers: `podman ps -a`
- Images: `podman images`

## Registry login (required)
```bash
podman login $REGISTRY_HOST
```
Trouble logging in? See Quickstart “Registry troubleshooting” or `docs/TROUBLESHOOTING.md`.

## Build, tag, push (manual flow)
```bash
# Build locally (from current dir)
podman build -t ${IMAGE_NAME}:${TAG} .

# Tag for your team namespace
podman tag ${IMAGE_NAME}:${TAG} ${REGISTRY_HOST}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${TAG}

# Push (if required by your flow)
podman push --tls-verify=false ${REGISTRY_HOST}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${TAG}
```

## Podman Compose (preferred)
```bash
# Build and start
podman-compose build
podman-compose up -d

# View logs
podman-compose logs -f

# Restart after changes
podman-compose up -d

# Stop services
podman-compose down
```

## Inspect & debug
```bash
# List containers (all)
podman ps -a

# Inspect a container
podman inspect <container_id_or_name>

# Exec into a running container
podman exec -it <container_id_or_name> /bin/sh
```

## Clean up (use carefully)
```bash
# Stop and remove all containers
podman rm -f $(podman ps -aq)

# Remove dangling images
podman image prune -f
```

## Healthcheck (optional)
- Example (compose, commented by default):
```yaml
# healthcheck:
#   test: ["CMD", "curl", "-fsS", "http://localhost:8080/health"]
#   interval: 30s
#   timeout: 3s
#   retries: 3
```
- Only enable if your app exposes `/health`.

## Multi-arch (Apple Silicon → amd64)
```bash
podman build --arch linux/amd64 -t ${IMAGE_NAME}:${TAG} .
```

## Common issues (quick)
- 401 on push/pull: re-run `podman login $REGISTRY_HOST`
- Port in use: change `PORT` in `.env` or stop the other process
- Compose not seeing `.env`: ensure file is named `.env` in the same folder
- DNS/connection errors: verify VPN/network and registry hostname

More fixes: `docs/TROUBLESHOOTING.md`.

## Remote work (MiniCloud)
- Provide your public SSH key to the host; they will add it to the correct team user
- SSH/SCP port: `30022`
- SSH: `ssh -p 30022 -i ~/.ssh/id_ed25519 teamX@<MINICLOUD_HOST>`
- SCP: `scp -P 30022 ./localfile teamX@<MINICLOUD_HOST>:/srv/containers/teamX/`
- Workspace: `cd /srv/containers/teamX`
- Then use `podman-compose build && podman-compose up -d`
- See team guides: `teams/team1/GUIDE.md`, `teams/team2/GUIDE.md`
