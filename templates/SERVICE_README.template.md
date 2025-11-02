# Service README (Template)

Briefly describe your service purpose and core responsibilities.

## Setup
1. Copy `Containerfile.template` to `Containerfile` and adjust base image and commands
2. Copy `container.env.template` to `.env` and set all variables
3. Build and run:
   ```
   podman login $REGISTRY_HOST
   podman-compose build
   podman-compose up -d
   ```

## Environment Keys
- `REGISTRY_HOST`, `REGISTRY_NAMESPACE`, `IMAGE_NAME`, `TAG`
- `TEAM_SUBDOMAIN`, `HOST_SUFFIX`
- `PORT`
- `LITEMAAS_BASE_URL`, `LITEMAAS_API_KEY`

## Endpoints
- `/` UI
- `/api/chat` Chat API (example)
- `/health` Health endpoint (recommended)

## Architecture Notes
- Keep it simple: one container service is enough for the hackathon
- Document decisions and tradeoffs in this README

## Demo URL
- `http://<TEAM_SUBDOMAIN>.<HOST_SUFFIX>`
