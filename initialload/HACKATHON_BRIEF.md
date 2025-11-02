# 🧭 One-Day Hackathon Brief — “Open Source Mentor Bot”

## 🎯 Objective
Build a **containerized chatbot** that acts as an *open-source mentor*.  
It should answer beginner questions about open source and Red Hat-style collaboration, using documentation you provide or generate.

Participants will gain hands-on experience with:
- **Podman containers** (local and remote registry usage)
- **Collaborative development** in isolated team environments
- **LLM-based APIs** (LiteMAAS / Llama-4-Scout-17B)
- **Open source best practices** aligned with Red Hat values

---

## 🕓 Schedule

| Time  | Activity |
|-------|-----------|
| 09:00 | Kickoff, introduction to Red Hat open culture |
| 09:30 | Team formation & ideation |
| 10:15 | **Phase 1:** Build base chatbot logic (connect to LiteMAAS) |
| 12:00 | Lunch & networking |
| 13:00 | **Phase 2:** Add documentation ingestion / retrieval features |
| 15:00 | **Phase 3:** Testing, documentation & polish |
| 16:00 | Team demos (5 min each) |
| 17:00 | Wrap-up & awards |

---

## ✅ Deliverables

Each team must present:
1. A running chatbot application accessible at `http://teamX.<IP>.sslip.io`
2. Clear documentation in their `README.md` explaining:
   - Purpose of the project
   - Architecture (web/api/data flow)
   - How Red Hat values are reflected in their work
3. Short (max 5 min) demo of the application

---

## 🧠 Judging Criteria (Total: 100 points)

| Category | Description | Points |
|-----------|--------------|--------|
| **Functionality** | Chatbot operates end-to-end, responds to input | 25 |
| **Openness & Documentation** | Clear, transparent, well-documented work | 20 |
| **Teamwork** | Visible collaboration and shared effort | 15 |
| **Red Hat Values** | Open collaboration, automation, community mindset | 20 |
| **Creativity & Impact** | Innovative or insightful use of tools | 20 |

---

## 🧰 Tools & Environment

- **Podman** and **Podman Compose** for container orchestration  
- Shared **registry** preconfigured at `registry.<IP>.sslip.io`  
- **Traefik** routes traffic via hostnames (`team1.<IP>.sslip.io`, etc.)  
- **LiteMAAS API** as the LLM backend (no OpenAI/Anthropic required)

Each team works inside:
```
/srv/containers/team1
/srv/containers/team2
```
They can connect with `podman login` and deploy containers directly.

---

## ❤️ Red Hat Values in Action

| Value | Behavior Example |
|--------|------------------|
| **Open Collaboration** | Share progress, reuse, and document everything |
| **Transparency** | Keep work visible and decisions documented |
| **Community First** | Optimize for collective learning, not individual success |
| **Automation** | Script or containerize every step |
| **Trust** | Respect teammates and embrace experimentation |

---

## 🏁 End Goal

By the end of the day, participants will have:
- Built and deployed a simple but working chatbot system
- Practiced open-source collaboration principles
- Understood container workflows (build, push, deploy)
- Learned how to integrate LLM APIs responsibly

