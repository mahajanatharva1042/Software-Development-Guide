# Software Development Guide — Full-Stack + Machine Learning

> A complete, practical handbook for planning, building, shipping, and maintaining a production-ready full-stack project with machine learning / AI features — plus a 150-item catalog of mistakes AI code generators make and how to prevent them.

[![Guide](https://img.shields.io/badge/guide-complete-brightgreen)](#)
[![Full-Stack](https://img.shields.io/badge/full--stack-Next.js%20%7C%20FastAPI%20%7C%20Postgres-blue)](#)
[![ML](https://img.shields.io/badge/ML-classical%20%7C%20deep%20%7C%20LLM%20%2F%20RAG-orange)](#)
[![AI Mistakes](https://img.shields.io/badge/AI%20mistakes-150%20cataloged-red)](#26-additional-ai-mistakes-by-root-cause-50-catalog)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](#license)

---

## What is this?

This repo is a **standalone development handbook** (not a starter template). It walks you from blank repo to production across:

- **Full-stack:** planning → stack choice → frontend → backend APIs → database → auth → uploads/storage
- **Machine learning:** classical ML / deep learning lifecycle (data → train → evaluate → serve → monitor) + LLM/RAG (retrieval, grounded prompts, fallback chains, evals)
- **Engineering:** build scripts, dependencies, testing pyramid, code quality, CI/CD, Docker, deployment, observability, security, performance, docs
- **AI safety:** **150 AI-generated-code mistakes** with wrong-vs-right fixes and review checklists, so you can use coding assistants without shipping their bugs

**Main file:** [`report.md`](./report.md) — the complete guide (~2,400 lines). Start there.

---

## Who is it for?

- Beginners building their first full-stack + ML project end-to-end
- Intermediate developers adding ML / LLM features to an existing web app
- Teams who want a shared checklist for reviews, CI, staging, and go-live
- Anyone using AI coding assistants who wants to catch hallucinations, security flaws, and logic errors before they merge

---

## How to use it

| Goal | Where to go |
|------|-------------|
| New project, top-to-bottom | Follow `report.md` §1 → §23 in order |
| Existing project, add ML | Jump to §10 (classical/deep) or §11 (LLM/RAG) |
| Harden before launch | §14 testing → §15 quality → §16 CI/CD → §20 security → Appendix C go-live |
| Review AI-generated PRs | §25 (100 mistakes) → §26 (50 root-cause mistakes) → Appendix D checklist + Appendix E guardrails |
| Copy-paste snippets | Appendix A (commands) + Appendix B (templates: `package.json`, error envelope, model card, PR template) |

> Tip: every section in `report.md` ends with a tick-box checklist. Copy the relevant ones into your issues/PRs.

---

## What's inside `report.md`

1. Mental model & project goals
2. Choosing your stack (TypeScript full-stack vs Python + JS split vs Python full-stack)
3. Repository & project structure (proven monorepo layout)
4. Environment setup (`env.example`, twelve-factor config)
5. Frontend (Server vs Client Components, data hooks, forms/validation)
6. Backend & API design (REST conventions, Node + FastAPI handler templates)
7. Database design (Postgres, migrations, RLS, indexing → pgvector path)
8. Auth (session, ownership, route protection, safe redirects)
9. Uploads & storage (validation, buckets, async processing jobs)
10. ML lifecycle (framing → data → training → eval → serving → MLOps)
11. LLM & RAG (grounded generation, chunking, provider fallback, refine modes, evals)
12. Build system & scripts
13. Dependency management
14. Testing (unit / integration / E2E / ML evals + examples)
15. Code quality & conventions
16. CI/CD (copy-paste GitHub Actions for web + ML)
17. Docker & Compose (multi-stage web + slim ML images)
18. Deployment (Vercel / Render / Cloud Run / Supabase / hosted ML)
19. Observability (errors, logs, metrics, uptime, drift)
20. Security checklist
21. Performance & scaling
22. Documentation (README / API / ML card / runbook)
23. Step-by-step build-from-scratch plan (Phase 0–6)
24. Common pitfalls & recommendations
25. **AI mistakes 1–100** (architecture / logic / security / env-testing)
26. **AI mistakes 101–150** (reasoning / output quirks / context blindness / bias-compliance / perf-tooling)
- Appendix A: commands cheat sheet
- Appendix B: templates (`package.json`, error envelope, model card, PR template)
- Appendix C: go-live checklist
- Appendix D: AI-code review checklist (PR gate)
- Appendix E: AI output guardrails (gitleaks, lint, CI gates)

---

## Repository structure

```
.
├── README.md        # You are here (repo homepage for GitHub)
├── report.md        # The complete guide — read this
├── env.example      # (Add) copy-paste env template from report.md §4.2
├── .github/
│   └── workflows/
│       └── ci.yml   # (Add) copy from report.md §16.1
└── LICENSE          # (Add) MIT — see below
```

> This repo intentionally ships docs-first. Add `env.example`, `ci.yml`, and `LICENSE` from the templates in `report.md` when you publish.

---

## Quickstart (using the guide for your project)

```bash
# 1. Clone this guide (read-only reference)
git clone <your-guide-repo-url>
cd software-development-guide

# 2. Read the handbook
# Open report.md — follow §23 Phase 0 (1-page scope) first

# 3. Scaffold YOUR app repo separately (example: Next.js + FastAPI ML)
npx create-next-app@latest my-app --typescript --tailwind --app
mkdir my-app/ml-service && cd my-app/ml-service
python -m venv .venv && source .venv/bin/activate
pip install fastapi uvicorn scikit-learn pytest

# 4. Copy standards from the guide into your app repo:
# - env.example (§4.2), package.json scripts (§12.1 / App. B.1)
# - API error envelope (§6.2 / App. B.2), model card (§10 / App. B.3)
# - CI workflow (§16.1), Dockerfiles (§17), review checklist (App. D)
```

---

## AI-mistakes catalog at a glance

| # | Category | Example |
|---|----------|---------|
| 1–25 | Architecture / system design | Missing pagination, N+1, no idempotency, JWT bloat |
| 26–50 | Code generation / logic | `var` closures, swallowed errors, unhandled promises |
| 51–75 | Security flaws | SQLi, XSS, `verify=False`, IDOR, open redirects |
| 76–100 | Env / dependency / testing | Fake packages, bloated images, flaky `sleep(5)` E2E |
| 101–110 | Logic / semantic root causes | Edge-case blindness, silent failures, inverted conditions |
| 111–120 | Syntax / output quirks | JSON-in-markdown, stub functions, bad imports |
| 121–130 | Context blindness | Duplicated utils, NFR misses, window drop-off |
| 131–140 | Security / bias / compliance | PII leaks, type juggling, copyright, compliance gaps |
| 141–150 | Perf / tooling / maintenance | Unindexed queries, stale deps, no eval set, skill erosion |

Rule: **AI proposes, humans dispose.** Every AI PR must pass typecheck + tests + Appendix D review + staging run.

---

## Contributing

Contributions welcome — especially new failure modes with minimal reproductions.

1. Fork → branch (`docs/add-<topic>` or `fix/<thing>`)
2. Keep sections checklist-terminated; include wrong-vs-right + how-to-catch
3. Update TOC + master map (§26.6) if adding mistakes
4. Open a PR using the template in `report.md` Appendix B.4

Please avoid committing secrets, binaries, or model weights. Docs and templates only unless discussed.

---

## License

MIT — free for personal and commercial use. Add a `LICENSE` file with the MIT text on publish. No warranty; verify all commands and security guidance for your environment.

---

## Roadmap

- [ ] Machine-readable checklists (`checklists/*.md`) for PR bots
- [ ] Minimal `examples/` (Next.js handler + FastAPI `/predict` + RAG retrieve)
- [ ] Translated summaries / visual architecture diagrams
- [ ] Versioned eval sets for LLM prompts

---

*Start with [`report.md`](./report.md). Start small, ship the loop, instrument everything, then scale what you measure.*
