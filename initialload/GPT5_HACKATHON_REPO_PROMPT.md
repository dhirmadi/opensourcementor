# 🧠 GPT-5 Prompt — Generate the Red Hat “Open Source Mentor Bot” Hackathon Starter Repository  
**For two teams using Podman (future OpenShift compatible)**

---

## 🎯 Objective

Create a complete **container-based starter repository** for a one-day hackathon called  
**“Open Source Mentor Bot”**.  
The goal is to help two student teams collaborate effectively in a shared Red Hat–style environment.

This prompt instructs GPT-5 to generate all necessary **folders, documentation, configuration templates, and environment files**, but **no application code**.

---

## ✅ Requirements

- **Podman-first**, vendor-neutral (use `Containerfile`, not `Dockerfile`).
- **No Docker terminology**.
- **No registry or Traefik configuration** (already done by organizers).
- Each team works only inside its folder:  
  `/srv/containers/team1` and `/srv/containers/team2`.
- Teams build and deploy using `podman` and `podman-compose`.
- Clear guidance, ready for students with limited DevOps experience.
- Should reflect **Red Hat values** and **best practices for open collaboration**.
- Structured for future **OpenShift migration**.

---

## 🏗️ Repository Layout to Generate

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

---

## 📦 Final Deliverable

GPT-5 should generate:
- All folders and files listed above.
- Markdown and YAML content as shown.
- No executable or service code — only scaffolding.

**After generation, print:**

```
✅ Hackathon starter repository created.
Each team can now work in /srv/containers/team1 or /srv/containers/team2,
build their containers, and deploy with `podman-compose up -d`.
Registry and routing are preconfigured.
```
