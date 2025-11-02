# Quickstart with Podman

This guide helps each team build and run their chatbot service using Podman and Podman Compose.

## Prerequisites
- Podman and Podman Compose installed
- Registry and Traefik preconfigured by organizers
- Your team folder: `teams/team1` or `teams/team2`

## 1) Move into your team folder
```
cd teams/team1   # or teams/team2
```

## 2) Copy templates and create `.env`
```
cp ../../templates/Containerfile.template ./Containerfile
cp ../../templates/container.env.template ./.env
```
Edit `.env` values to match your team and environment.

## 3) Login to the registry
```
podman login $REGISTRY_HOST
```

## 4) Build and run with Compose
```
podman-compose build
podman-compose up -d
```

## 5) Access your app
```
http://<TEAM_SUBDOMAIN>.<HOST_SUFFIX>
```
Example: `http://team1.<IP>.sslip.io`

## 6) Iteration loop
- Update your code and configuration
- Rebuild when necessary: `podman-compose build`
- Restart services: `podman-compose up -d`
- View logs: `podman-compose logs -f`

## Registry troubleshooting (quick tips)
- "unauthorized: authentication required": run `podman login $REGISTRY_HOST` again
- DNS/connection errors: verify network/VPN and the registry hostname
- Permission denied on push: check `REGISTRY_NAMESPACE` and your access

See also: `docs/TROUBLESHOOTING.md` for more.

## Tips
- Keep services simple; one primary web container is enough for the hackathon
- Use environment variables (see `.env`) and do not commit secrets
- Document as you go in your team `README.md`

## Remote access (MiniCloud)
- Provide your public SSH key to the host for your team user (team1/team2)
- SSH/SCP port: `30022`
- SSH: `ssh -p 30022 teamX@<MINICLOUD_HOST>`
- SCP: `scp -P 30022 ./localfile teamX@<MINICLOUD_HOST>:/srv/containers/teamX/`
- Workspace on server: `/srv/containers/teamX`
- Recommended: use `podman-compose` for build/run
