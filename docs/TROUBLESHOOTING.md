# Troubleshooting

Quick fixes for common issues during the hackathon.

## Registry issues
- "unauthorized: authentication required"
  - Run: `podman login $REGISTRY_HOST` and re-enter credentials
- "connection refused" or DNS errors
  - Verify VPN/Network and registry hostname correctness
- Push fails due to permissions
  - Check `REGISTRY_NAMESPACE` in `.env`, confirm you have access

## Build issues
- "no such file or directory"
  - Ensure templates are copied into team folder (`Containerfile`, `.env`)
- "permission denied"
  - Check file perms, try `chmod +x` for scripts if any
- Build on Apple Silicon for amd64
  - Add: `podman build --arch linux/amd64 -t $IMAGE .`

## Runtime issues
- Container exits immediately
  - Check logs: `podman-compose logs app`
- Port already in use (8080)
  - Stop conflicting process or change `PORT` in `.env`
- Service healthy check failing
  - Disable healthcheck or add `/health` endpoint in your app

## LiteMAAS issues
- 401 Unauthorized
  - Verify `LITEMAAS_API_KEY` in `.env`
- Timeouts/slow responses
  - Increase client timeout to 10–15s; retry with backoff
- 429 Rate limit
  - Add exponential backoff (e.g., 250ms → 1s → 2s)

## Diagnostics quick commands
- Containers: `podman ps -a` | Logs: `podman-compose logs -f`
- Rebuild: `podman-compose build` | Restart: `podman-compose up -d`
- Inspect image: `podman inspect <image>`
