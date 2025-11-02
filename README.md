# Open Source Mentor Bot — Hackathon Starter

A Podman-first, vendor-neutral starter repository for the one-day “Open Source Mentor Bot” hackathon. This repo provides scaffolding (no application code) so teams can get to building quickly while practicing Red Hat–style open collaboration.

This repository follows the brief in `HACKATHON_BRIEF.md`.

## What You’ll Build Today
- A containerized chatbot that answers beginner questions about open source and Red Hat-style collaboration
- Uses LiteMAAS as the LLM backend (no OpenAI/Anthropic required)
- Deployed with Podman and Podman Compose

## Repository Layout
```
open-source-mentor-bot/
├── README.md
├── HACKATHON_BRIEF.md
├── CONTRIBUTING.md
├── .gitignore
├── LICENSE
├── docs/
│   ├── QUICKSTART_PODMAN.md
│   ├── REDHAT_VALUES.md
│   ├── FUTURE_OPENSHIFT.md
│   ├── ARCHITECTURE_OVERVIEW.md
│   └── LITEMAAS_REFERENCE.md
├── templates/
│   ├── Containerfile.template
│   ├── container.env.template
│   └── SERVICE_README.template.md
└── teams/
    ├── team1/
    │   ├── compose.yaml
    │   ├── .env.example
    │   └── README.md
    └── team2/
        ├── compose.yaml
        ├── .env.example
        └── README.md
```

## Quick Start (Teams)
Follow `docs/QUICKSTART_PODMAN.md` for full details. TL;DR:

1) Choose your team folder:
```
cd teams/team1   # or teams/team2
```

2) Copy templates and create your `.env`:
```
cp ../../templates/Containerfile.template ./Containerfile
cp ../../templates/container.env.template ./.env
```

3) Edit `.env` (set registry, subdomain, LiteMAAS settings). Example keys:
- REGISTRY_HOST, REGISTRY_NAMESPACE, IMAGE_NAME, TAG
- TEAM_SUBDOMAIN, HOST_SUFFIX
- LITEMAAS_BASE_URL, LITEMAAS_API_KEY

4) Build and run:
```
podman login $REGISTRY_HOST
podman-compose build
podman-compose up -d
```

5) Access your app at:
```
http://<TEAM_SUBDOMAIN>.<HOST_SUFFIX>
```
Example: `http://team1.<IP>.sslip.io`

Quick reference for commands: see `docs/CHEATSHEET_PODMAN.md`.

## Red Hat Values in Action
- Open collaboration, transparency, community-first, automation, trust
- Document as you go and keep changes small, frequent, and reviewable
See `docs/REDHAT_VALUES.md` for concrete behaviors.

## Architecture Overview
An example architecture and responsibilities are outlined in `docs/ARCHITECTURE_OVERVIEW.md` to help guide your design. You control the actual implementation.

## Future OpenShift
This repo is structured for a smooth migration to OpenShift. See `docs/FUTURE_OPENSHIFT.md` for pointers.

## Multi-arch note (Apple Silicon)
If you need an amd64 image from an ARM64 host (e.g., Apple M-series):
```
podman build --arch linux/amd64 -t ${IMAGE_NAME}:${TAG} .
```
Use only if required by your runtime environment.

## Prevent committing secrets
A pre-commit hook is provided to block accidental commits of secrets. Enable it once per clone:
```
git config core.hooksPath .githooks
chmod +x .githooks/pre-commit
```
The hook scans staged files for common patterns (API keys, private keys, bearer tokens, `.env` files) and blocks the commit with guidance if found.

## Contributing
See `CONTRIBUTING.md`. Use issues and PRs even within teams to model open collaboration.

## License
See `LICENSE`.


