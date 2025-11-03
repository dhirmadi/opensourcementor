## Hackathon FAQ — Open Source Mentor Bot

This FAQ captures common questions and the current answers from the organizers. Anything marked as TBD will be provided or finalized at kickoff. Teams can update this file as details are confirmed.

### Logistics and Teams

- **How are teams formed and what size should they be?**
  - Teams form on-site at 09:30 during the agenda. Typical size is 3-4 people, but align with your table/coach. Final composition: TBD at kickoff.

- **Are there interim checkpoints besides the 16:00 demo?**
  - Informal check-ins align to the phases in the brief: Phase 1 (10:15), Phase 2 (13:00), Phase 3 (15:00). The only hard deadline is demos at 16:00.

- **What communication channel will we use for announcements/help?**
  - We will handle announcements and support in-room verbally. Please approach a coach for help.

### Infrastructure and Access

- **What is the shared <IP> used for `*.sslip.io` hostnames?**
  - TBD. We will announce `<IP>` at kickoff. Access pattern: `http://teamX.<IP>.sslip.io`.

- **Where do teams work on the remote host?**
  - `/srv/containers/team1` and `/srv/containers/team2` (see brief). Recommended: use `podman-compose` in that directory.

- **How do we access the remote host (MiniCloud)?**
  - SSH and SCP via port `30022` (see Quickstart). Example:
    - SSH: `ssh -p 30022 teamX@<MINICLOUD_HOST>`
    - SCP: `scp -P 30022 ./localfile teamX@<MINICLOUD_HOST>:/srv/containers/teamX/`
    - Workspace: `/srv/containers/teamX`

- **What are the registry credentials and limits?**
  - Registry: `registry.<IP>.sslip.io` (from brief). Credentials and any limits: TBD at kickoff. Login with `podman login $REGISTRY_HOST`.

- **Are there hardware limits (CPU/RAM/GPU)?**
  - GPU: none planned. CPU/RAM: It's an M4 MacBook Pro. Keep containers lightweight and mindful of shared resources.
  <!-- Clarify hardware: e.g., "MacBook Pro (M4), 12 CPU cores, 36–48GB RAM" and any per-team caps. If shared, note contention expectations. Also fix grammar: "It's an M4 MacBook Pro." -->

- **Can we persist data?**
  - Yes. Use volumes and files under `/srv/containers/teamX`. Avoid storing secrets in git; prefer `.env` and mounted files.

### LiteMAAS / Model Usage

- **What LLM backend do we use?**
  - LiteMAAS with Llama-4-Scout-17B (see brief). No OpenAI/Anthropic keys required. If you want to build an abstraction layer to allow for different MaaS services, consider a thin adapter around LiteMAAS first to avoid over-engineering.
  <!-- Scope caution: abstraction layers are great, but timebox them. Recommend thin adapter around LiteMAAS first; avoid over-engineering given the 1-day scope. Define minimal interface boundaries up front. -->

- **What is the API endpoint and auth method?**
  - Endpoint URL and token will be provided at kickoff. Expect bearer-token style auth; see `docs/LITEMAAS_REFERENCE.md`.
  - Example request:
    ```bash
    curl -sS \
      -H "Authorization: Bearer $LITEMAAS_TOKEN" \
      -H "Content-Type: application/json" \
      -d '{"model":"llama-4-scout-17b","messages":[{"role":"user","content":"Hello"}]}' \
      "$LITEMAAS_API_BASE/chat/completions"
    ```
  <!-- Typos/links: remove extra space before period and fix filename to `docs/LITEMAAS_REFERENCE.md`. Consider adding a one-line example (curl) and required headers to reduce setup friction. -->

- **What are the model limits (context window, tokens, streaming, tool-calling)?**
  - Assume standard chat completions, with streaming likely supported. Start simple; add advanced features if time permits. Size text chunks to roughly 512–1024 tokens and keep prompts concise for reliability.
  <!-- If available, add concrete limits (e.g., max input tokens/context window) so teams size chunks/embeddings appropriately. -->

- **Any per-team rate limits or quotas?**
  - Implement exponential backoff with jitter (e.g., 200ms × 2^n, max 3–5 retries) and add a simple circuit breaker on sustained 429/5xx responses.
  <!-- Recommend a default: exponential backoff (e.g., 200ms * 2^n, jitter), max retries 3–5, and circuit-breaker after sustained 429/5xx to protect the service. -->

### Data and RAG

- **What documentation can we ingest?**
  - Use repo-provided docs and materials you generate (see brief). External public docs are allowed if cited and stored locally in your container or volume. Avoid sending sensitive data to third parties.

- **Is there a cap on ingested data/index size?**
  - Keep indices modest for performance and fairness (as guidance: ≤ 20MB total text, ≤ 1,000 chunks, chunk size ~512–1024 tokens).
  <!-- Provide guidance: e.g., "≤ 20MB total ingested text, ≤ 1k chunks, chunk size ~512–1024 tokens". This helps teams make trade-offs. -->

- **Are we allowed to call external services?**
  - Prefer the provided LiteMAAS backend. External LLM APIs are discouraged and may be blocked by network policy. Non-LLM external docs (static fetch) are fine if cached locally in your container/volume.
  <!-- Clarify policy: are outbound calls to third-party LLMs strictly disabled, or merely discouraged? If disabled, say "No external LLM calls" explicitly. Encourage caching docs to avoid rate/availability issues. -->

### App Requirements and Domains

- **What URL and protocol should we expose?**
  - Traefik routes traffic via hostnames using `sslip.io` (see brief). Expose HTTP on port 8080 inside your container. Access via `http://teamX.<IP>.sslip.io`. If HTTPS is enabled, TLS is terminated by Traefik at the edge.
  <!-- Confirm scheme: should teams expect HTTPS at the edge (https://teamX.<IP>.sslip.io)? If so, note that TLS is terminated by Traefik and apps should serve plain HTTP internally. -->

- **Do we need health/readiness endpoints?**
  - Optional but recommended: provide `/health` or `/healthz`. Compose includes a commented healthcheck example; ensure your image has `curl` (or adapt to `wget`/busybox) to avoid false failures.
  <!-- Ensure the container actually has `curl` (or use `CMD-SHELL` with `wget`/`busybox`), otherwise the sample healthcheck will fail. -->

- **What is the minimum UX for the chatbot?**
  - A simple web UI that accepts a question and returns an answer. Phase 2 adds documentation ingestion/RAG. Showing sources/citations is encouraged but not strictly required for judging.

### Containers, Compose, and Traefik

- **Is there a compose template and expected labels?**
  - Yes. See `teams/teamX/compose.yaml`. Traefik labels are provided as commented examples:
    - `traefik.enable=true`
    - `traefik.http.routers.mentorbot.rule=Host(\`${TEAM_SUBDOMAIN}.${HOST_SUFFIX}\`)`
    - `traefik.http.services.mentorbot.loadbalancer.server.port=8080`
  - If label-based routing is enabled, set `TEAM_SUBDOMAIN` (e.g., `team1`) and `HOST_SUFFIX` (e.g., `<IP>.sslip.io`) in `.env` and/or labels. Use unique router/service names per team (e.g., `mentorbot-team1`) to avoid collisions on shared hosts.
  <!-- Suggest per-team unique router/service names (e.g., mentorbot-team1) to avoid global label collisions if multiple stacks run on the same host. -->

- **What is the registry/image naming convention?**
  - Compose uses `image: ${REGISTRY_HOST}/${REGISTRY_NAMESPACE}/${IMAGE_NAME}:${TAG}`. Push only if your workflow requires pulling from the registry; when building directly on the host, local images are sufficient. If the registry uses a self-signed cert, you may need `--tls-verify=false` (see Cheatsheet).
  <!-- Align with Cheatsheet: pushing examples use `--tls-verify=false`. Confirm registry cert/trust so teams know whether to include or omit that flag. -->

- **What base images are approved? Any security constraints?**
  - Use minimal, well-maintained base images (e.g., UBI or Alpine) suitable for your stack. Run as non-root where possible and drop unnecessary capabilities.
  <!-- Recommend running as non-root and dropping unnecessary capabilities. UBI minimal + distro packages or Alpine + pinned versions are good defaults. -->

- **Does Apple Silicon require special flags?**
  - If you need amd64: `podman build --arch linux/amd64 -t ${IMAGE_NAME}:${TAG} .` (see Cheatsheet). Prefer building on the remote host to avoid cross-arch issues.
  <!-- Tip: building on the remote host avoids cross-arch issues. Prefer that path if available. -->

### Judging and Demo

- **What are the deliverables?**
  - A running chatbot at `http://teamX.<IP>.sslip.io`, a clear `README.md` (purpose, architecture, Red Hat values), and a 5-minute demo (see brief). Link this FAQ from your README for easy discovery.
  <!-- Consider linking this FAQ from the root `README.md` and team `README.md` so participants find it quickly. -->

- **Do we need to demonstrate doc ingestion?**
  - The day’s Phase 2 focuses on ingestion/RAG. Aim to show retrieval grounded in your docs, even if simple.

- **How are “Red Hat Values” scored?**
  - We look for open collaboration (sharing, reuse), transparency (visible docs/decisions), community mindset (help others), automation (scripts/containers), and trust (respectful teamwork). Concrete examples include: publishing work-in-progress notes, reusing templates, adding simple automation (build/run scripts), documenting decisions/trade-offs, and helping neighboring teams.
  <!-- Add concrete examples: publish work-in-progress notes, reuse templates, automate build/run scripts, document decisions/trade-offs, help neighboring teams. -->

- **What artifacts beyond README help our score?**
  - A lightweight architecture diagram in your README, a short GIF/video of the flow, and clear notes on values and trade-offs.

### Security and Compliance

- **Will images be scanned? Any CVE policy?**
  - Best effort: keep images minimal, pin versions, avoid root where possible. If tools like Trivy are available, run scans locally before demos.
  <!-- If scanners (e.g., Trivy) are available, note whether they will be run before demos and any severity thresholds. -->

- **How should we manage secrets?**
  - Use `.env` (do not commit). Mount secret files or use environment variables. Rotate tokens if compromised; notify organizers.

### IP and Licensing

- **What license should we use?**
  - This repository includes GPL-3.0 (see `/LICENSE`). Teams may retain it for their work unless otherwise instructed. If you choose another license, ensure compatibility and document your rationale.
  <!-- Specify license explicitly for clarity: this repository includes GPL-3.0 (see `/LICENSE`). If teams choose otherwise, ensure compatibility and document rationale. -->

- **Any dependency/license restrictions (e.g., GPL/AGPL)?**
  - Prefer permissive/open-source licenses compatible with distribution. Document any notable choices in your README.

### Mentorship and Support

- **How do we request mentor help?**
  - Approach a mentor in the room. Mentors will also do periodic walk-throughs aligned to phases.

- **Are there office hours for LiteMAAS/Traefik/Podman?**
  - Yes—short walkthroughs in Phase 1 and Phase 2. Support available during the entire workshop.

### Contingencies

- **What if the registry, Traefik, or LiteMAAS is degraded?**
  - We will announce workarounds. Examples: build/run locally on the host without pulling; use direct container port mapping; switch to a local mocked LLM stub while the backend recovers.

- **Is offline or degraded-mode development acceptable?**
  - Yes. Prioritize delivering the core UX and architecture; document any constraints and what you would do with more time.

---

#### Quick References

- Team workspace: `/srv/containers/teamX`
- Access URL: `http://teamX.<IP>.sslip.io`
- Registry: `registry.<IP>.sslip.io` (login with `podman login $REGISTRY_HOST`)
- Compose basics:
  ```bash
  podman-compose build
  podman-compose up -d
  podman-compose logs -f
  ```
- Optional healthcheck (enable only if your app exposes `/health`):
  ```yaml
  # healthcheck:
  #   test: ["CMD", "curl", "-fsS", "http://localhost:8080/health"]
  #   interval: 30s
  #   timeout: 3s
  #   retries: 3
  ```


