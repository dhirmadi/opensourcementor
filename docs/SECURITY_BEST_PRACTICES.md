# Security Best Practices (Hackathon Scope)

Practical steps to stay safe during the event.

## Image security
- Use UBI minimal images when possible
- Scan images before deployment (if available): `podman scan <image>`
- Avoid `:latest` in production (pin tags)

## Secret management
- Keep API keys in `.env` (never commit secrets)
- Rotate keys after the event
- Prefer Podman/Kubernetes Secrets for long-term use

## Runtime security
- Run as non-root if your stack allows
- Prefer read-only root filesystem where possible
- Limit container capabilities (default rootless is fine for hackathon)

## Network security
- Use private networks for inter-service traffic where possible
- Use TLS for external APIs when available

## LLM-specific considerations
- Do not log API keys or sensitive prompts
- Sanitize user inputs; apply basic content filtering if feasible
- Add rate limiting/backoff to avoid abuse
