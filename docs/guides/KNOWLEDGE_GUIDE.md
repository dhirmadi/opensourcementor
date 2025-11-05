# Knowledge Contribution Guide

Hackathon teams may contribute knowledge in multiple formats. Keep it simple and useful.

## Accepted formats
- Markdown (`.md`) and plain text (`.txt`)
- Images/diagrams: `.png`, `.jpg`, `.svg`, `.drawio`
- Slide decks and docs: `.pdf`, `.pptx`, `.odp`
- Spreadsheets: `.csv`, `.xlsx` (keep small)
- Notebooks: `.ipynb` (ensure reproducible; avoid large outputs)
- Code samples: put code in repo and reference from docs
- Links: reference external resources in markdown

## Where to put things
- Use `docs/knowledge/` for all team-produced know-how
- Create subfolders if needed, e.g., `docs/knowledge/rag/`, `docs/knowledge/podman/`

## Naming and organization
- Prefer short, descriptive filenames: `liteMAAS-tips.md`, `routing-diagram.drawio`
- Add a brief intro at the top: purpose, audience, last updated date
- Keep binaries small (<20 MB). If larger, consider linking or Git LFS (optional)

## Good practices
- Prefer markdown summaries alongside binary files (what is it, how to use it)
- Avoid secrets in documents or screenshots
- For notebooks: clear outputs before committing if large; include environment hints
- For diagrams: include the source (`.drawio`) and an exported image (`.png`)

## How to reference
- From team READMEs, link to files under `docs/knowledge/`
- From code, link back to relevant knowledge files for context

## Review & contributions
- Open a PR with a short description of what you’re adding and why
- Small, frequent contributions are better than big drops
