# Action Plan — Architect Review (2025-11-02)

Source: `docs/report/review20251102.md`
Scope: Hackathon-focused improvements (no overengineering). Deliver fast wins that unblock teams and improve reliability, security hygiene, and clarity.

## Objectives
- Reduce friction to first successful deploy
- Provide minimal but effective security and reliability guidance
- Preserve vendor-neutral, Podman-first approach and Red Hat values

## Summary of Critical Findings (from review)
- Missing troubleshooting guidance (registry, build, runtime, LiteMAAS)
- Minimal security posture; no consolidated best-practices doc
- LiteMAAS reference lacks production patterns (timeouts, retries, privacy)
- Health checks not wired in Compose (only commented in Containerfile)
- Observability, CI/CD patterns out of scope but should have stubs
- Pre-demo readiness checklist missing; E2E validation not documented

## Deliverables (Minimal, High-Impact)
1) Troubleshooting guide: `docs/TROUBLESHOOTING.md`
2) Security hygiene guide: `docs/SECURITY_BEST_PRACTICES.md` (hackathon scope)
3) LiteMAAS production notes: extend `docs/LITEMAAS_REFERENCE.md`
4) Healthcheck guidance: add to `teams/*/compose.yaml` (doc + example)
5) Quickstart updates: registry troubleshooting section
6) Demo Day Checklist: add to `teams/*/README.md`
7) Future OpenShift: add an "Advanced Patterns" section (short)
8) Optional stubs (post-hackathon ready, safe to skip during event):
   - `docs/OBSERVABILITY.md` (1-pager)
   - `docs/CI_CD_PATTERNS.md` (1-pager)

## Plan by Priority (Do Now vs Later)

### High Priority — Complete before hackathon (≤ 3 hours total)
- [ ] Create `docs/TROUBLESHOOTING.md`
  - Registry auth errors, connection refused, build not found, permission denied, container restarts, port conflicts, LiteMAAS 401/timeouts
  - Include exact commands (`podman login`, `podman ps`, `podman-compose logs -f`)
- [ ] Update `docs/QUICKSTART_PODMAN.md`
  - Add short "Registry troubleshooting" subsection
  - Add short "Logs and restart" reminder
- [ ] Add healthcheck example to `teams/*/compose.yaml` (commented by default)
  - Example:
    ```yaml
    # healthcheck:
    #   test: ["CMD", "curl", "-fsS", "http://localhost:8080/health"]
    #   interval: 30s
    #   timeout: 3s
    #   retries: 3
    ```
  - Note: keep commented to avoid breaking teams without a `/health` endpoint
- [ ] Extend `docs/LITEMAAS_REFERENCE.md`
  - Add sections: Error handling (timeouts, 429 backoff), Security (don’t log keys, sanitize prompts), Observability (latency, error rate)
- [ ] Add Demo Day Checklist to `teams/team1/README.md` and `teams/team2/README.md`
  - Access URL works; README explains architecture; logs are clean; values examples; 5-min demo script
- [ ] Add `docs/SECURITY_BEST_PRACTICES.md` (hackathon scope)
  - Image scanning mention, non-root, secrets handling, rotate keys post-event
- [ ] Add note re: Apple Silicon / multi-arch in `README.md`

### Medium Priority — If time permits (≤ 1 hour)
- [ ] Add `docs/FUTURE_OPENSHIFT.md` "Advanced Patterns" section (brief)
  - ImageStreams, simple NetworkPolicy mention, ResourceQuotas/LimitRanges pointers

### Low Priority — Post-hackathon
- [ ] Add `docs/OBSERVABILITY.md` one-pager (logging/metrics/tracing basics)
- [ ] Add `docs/CI_CD_PATTERNS.md` one-pager (Tekton/Jenkins/GitOps pointers)
- [ ] Optional sample app scaffold in `examples/` (Flask/FastAPI + LiteMAAS)

## Task Breakdown with Ownership & Effort
- Troubleshooting guide — Owner: Mentors; Effort: 45 min
- Quickstart updates — Owner: Mentors; Effort: 15 min
- Compose healthcheck comments — Owner: Mentors; Effort: 15 min
- LiteMAAS reference enhancements — Owner: API-focused mentor; Effort: 30 min
- Demo Day Checklist additions — Owner: Mentors; Effort: 15 min
- Security best practices (hackathon) — Owner: Security-minded mentor; Effort: 30 min
- Multi-arch note in README — Owner: Mentors; Effort: 10 min
- Future OpenShift advanced patterns — Owner: Platform mentor; Effort: 20 min (optional)

Total pre-hackathon effort: ~2.5–3 hours

## Acceptance Criteria
- Students can recover from common failures using `docs/TROUBLESHOOTING.md`
- Teams understand minimal security hygiene and do not commit secrets
- LiteMAAS guidance covers timeouts/retries and input/output safety basics
- Healthcheck patterns are visible and non-breaking (commented examples)
- Each team has a clear Demo Day checklist in their README
- Quickstart includes a registry troubleshooting note

## Edit Map (Where to Change What)
- Add: `docs/TROUBLESHOOTING.md`
- Add: `docs/SECURITY_BEST_PRACTICES.md`
- Update: `docs/QUICKSTART_PODMAN.md` (Registry troubleshooting subsection)
- Update: `docs/LITEMAAS_REFERENCE.md` (Error handling, Security, Observability)
- Update: `teams/team1/README.md`, `teams/team2/README.md` (Demo Day Checklist)
- Update: `teams/team1/compose.yaml`, `teams/team2/compose.yaml` (commented healthcheck)
- Update: `README.md` (multi-arch note for Apple Silicon)
- Update (optional): `docs/FUTURE_OPENSHIFT.md` (Advanced Patterns)

## Risks & Mitigations (Hackathon-Specific)
- Registry auth blocks progress → Mitigated by Quickstart/Troubleshooting updates
- LiteMAAS latency/timeouts → Mitigated by timeout/backoff guidance
- Port conflicts and container restarts → Mitigated by Troubleshooting steps
- Overengineering risk → All changes are documentation-first and optional examples only

## Go/No-Go
- Go: Proceed with High Priority items immediately
- Defer: Medium/Low priority to available mentor bandwidth
