# Complete Guide to Developing a Full-Stack Project with Machine Learning Integration

**Document type:** Standalone development handbook
**Audience:** Beginners to intermediate developers building a production-ready full-stack app with ML / AI features
**Scope:** Planning → stack selection → frontend → backend → database → auth → ML lifecycle → LLM/RAG → testing → CI/CD → Docker → deployment → monitoring
**Last updated:** 2026-10-16

> How to use this file: follow top-to-bottom for a first project. For an existing project, jump to the section you need. Every section ends with an actionable checklist. Copy-paste commands and templates are provided throughout.

---

## Table of Contents

1. [Mental Model & Project Goals](#1-mental-model--project-goals)
2. [Choosing Your Stack](#2-choosing-your-stack)
3. [Repository & Project Structure](#3-repository--project-structure)
4. [Environment Setup & Configuration](#4-environment-setup--configuration)
5. [Frontend Development](#5-frontend-development)
6. [Backend Development & API Design](#6-backend-development--api-design)
7. [Database Design](#7-database-design)
8. [Authentication & Authorization](#8-authentication--authorization)
9. [File Uploads & Storage](#9-file-uploads--storage)
10. [Machine Learning Lifecycle (Classical ML / Deep Learning)](#10-machine-learning-lifecycle-classical-ml--deep-learning)
11. [LLM & RAG Integration (Modern AI Features)](#11-llm--rag-integration-modern-ai-features)
12. [Build System & Scripts](#12-build-system--scripts)
13. [Dependency Management](#13-dependency-management)
14. [Testing Strategy](#14-testing-strategy)
15. [Code Quality & Conventions](#15-code-quality--conventions)
16. [CI/CD Pipeline](#16-cicd-pipeline)
17. [Docker & Containerization](#17-docker--containerization)
18. [Deployment](#18-deployment)
19. [Observability, Logging & Monitoring](#19-observability-logging--monitoring)
20. [Security Checklist](#20-security-checklist)
21. [Performance & Scaling](#21-performance--scaling)
22. [Documentation](#22-documentation)
23. [Step-by-Step: Build From Scratch](#23-step-by-step-build-from-scratch)
24. [Common Pitfalls & Recommendations](#24-common-pitfalls--recommendations)
25. [AI Mistakes While Developing Software (100 Catalog)](#25-ai-mistakes-while-developing-software-100-catalog)
26. [Additional AI Mistakes by Root Cause (50 Catalog)](#26-additional-ai-mistakes-by-root-cause-50-catalog)
27. [Appendix A: Commands Cheat Sheet](#appendix-a-commands-cheat-sheet)
28. [Appendix B: Templates](#appendix-b-templates)
29. [Appendix C: Go-Live Checklist](#appendix-c-go-live-checklist)
30. [Appendix D: AI-Code Review Checklist](#appendix-d-ai-code-review-checklist)
31. [Appendix E: AI Output Guardrails](#appendix-e-ai-output-guardrails)

---

## 1. Mental Model & Project Goals

### 1.1 What "full-stack + ML" means

```
┌──────────┐      ┌──────────┐      ┌──────────┐      ┌──────────┐
│ Frontend │ ───▶ │ Backend  │ ───▶ │ Database │      │ ML Layer │
│ (UI/UX)  │ ◀─── │ (API)    │ ◀─── │ (state)  │ ◀──▶ │ (predict)│
└──────────┘      └──────────┘      └──────────┘      └──────────┘
     │                  │                 │                  │
     └──────────────────┴─────────────────┴──────────────────┘
                              Users / Data flow
```

- **Frontend:** what the user sees and interacts with.
- **Backend:** business logic, auth, validation, orchestration.
- **Database + Storage:** persistent state (relational data, files, vectors).
- **ML layer:** one or more of:
  - (a) Classical ML (classification, regression, recommendation, clustering),
  - (b) Deep learning (vision, NLP, audio),
  - (c) LLM features (chat, summarization, Q&A, agents, RAG).

### 1.2 Define scope before coding

Answer these in writing (1 page max) before creating the repo:

| Question | Example answer |
|----------|----------------|
| Problem | Students cannot revise scattered PDFs efficiently |
| User | Logged-in student with courses and uploads |
| Core loop | Upload → organize → ask AI → quiz → revise |
| Non-goals (v1) | No real-time collaboration, no mobile app, no payments |
| Success metric | Upload→quiz in < 60s, AI answers cite sources |
| ML success metric | Precision/recall or grounded-answer rate, latency p95 |

**Checklist:**

- [ ] One-paragraph problem statement written
- [ ] 3–5 core user stories listed
- [ ] Explicit non-goals for v1
- [ ] Measurable success criteria (product + ML)

---

## 2. Choosing Your Stack

There is no single best stack. Pick for familiarity, hiring, hosting, and ML needs.

### 2.1 Recommended starter stacks

| Layer | Option A: TypeScript Full-Stack (recommended for web-first) | Option B: Python + JS Split | Option C: Python Full-Stack |
|-------|-------------------------------------------------------------|------------------------------|------------------------------|
| Frontend | Next.js 14+ (App Router) + React + Tailwind CSS | Same (Next.js) | HTMX + Jinja / Streamlit (prototypes only) |
| Backend API | Next.js Route Handlers (`app/api/**/route.ts`) | FastAPI (Python) for ML + Next.js for web | FastAPI / Django + DRF |
| DB | Postgres (Supabase / Neon / self-hosted) | Same | Same |
| Auth | Supabase Auth / Auth.js / Clerk | Same | Same |
| Storage | S3-compatible (Supabase Storage / AWS S3) | Same | Same |
| Classical ML | Python microservice or ONNX in Node | scikit-learn / XGBoost in FastAPI | Same |
| Deep learning | Python service (PyTorch) + API call | Same | Same |
| LLM | Provider APIs (OpenAI / Anthropic / Groq / OpenRouter / Gemini) via backend only | Same | Same |
| Deploy | Vercel (web) + Render/Fly (Python) + Supabase (data) | Same | Docker on VPS / cloud |

**Rule of thumb:**

- If your product is web-first with light ML (LLM prompts, keyword scoring, small classifiers) → **Option A**. Keep everything in one repo, one language.
- If you train custom models, do image/audio/video inference, or need numpy/pandas/torch → **Option B**. Web app calls a Python ML service over HTTP.
- Avoid running heavy training inside the request path. Train offline, serve via API.

### 2.2 Decision table

| Need | Choose |
|------|--------|
| SEO + fast iteration + one repo | Next.js App Router |
| Complex relational data + RLS | Postgres |
| Need auth + storage + realtime quickly | Backend-as-a-service (Supabase / Firebase) |
| Custom ML training | Python 3.10+, scikit-learn / PyTorch / TensorFlow |
| LLM chat / summarization / RAG | Backend-routed provider calls with fallback chain (never expose keys in frontend) |
| Vector search at scale | pgvector / Qdrant / Pinecone; start with Postgres `tsvector` or pgvector |

**Checklist:**

- [ ] Stack chosen and justified in README
- [ ] Clear boundary: what runs in Node vs Python (if both)
- [ ] Hosting targets identified (web, API, DB, ML service)

---

## 3. Repository & Project Structure

### 3.1 Monorepo vs polyrepo

- **Solo / small team, v1:** monorepo (one repo: `web/` + `ml-service/` + `supabase/`). Simpler CI, atomic changes.
- **Scale later:** split ML service to its own repo/service when deploy cycles diverge.

### 3.2 Proven layout (works for Option A + B)

```
project-root/
├── app/                        # Frontend pages + API routes (Next.js App Router)
│   ├── page.tsx                # Public landing (/)
│   ├── layout.tsx              # Root layout (fonts, metadata, providers)
│   ├── globals.css
│   ├── (auth)/                 # Public auth group: signin/, signup/
│   ├── (app)/                  # Protected group: dashboard/, courses/, etc.
│   │   └── layout.tsx          # Sidebar + Topbar shell
│   └── api/                    # Backend endpoints (thin handlers)
│       ├── health/route.ts
│       ├── items/route.ts
│       ├── uploads/route.ts
│       ├── ml/predict/route.ts       # Proxies to Python ML service
│       └── ai/ask/route.ts           # LLM grounded Q&A
├── components/
│   ├── ui/                     # Design-system primitives (button, card, input, dialog)
│   ├── shell.tsx               # App chrome (sidebar, topbar)
│   └── charts/, forms/, etc.
├── hooks/                      # Client data hooks (use-items, use-uploads)
├── lib/
│   ├── utils.ts                # cn(), format helpers
│   ├── validation.ts           # Zod schemas shared by client + server
│   ├── api.ts                  # Typed fetch wrappers
│   └── ml/
│       ├── features.ts         # Feature building (shared logic, no secrets)
│       └── prompts.ts          # LLM system prompts + refine modes
├── utils/
│   └── db/
│       ├── client.ts           # Browser DB client
│       ├── server.ts           # Server DB client (cookies)
│       └── service.ts          # Privileged service client (server-only)
├── ml-service/                 # Python ML service (Option B; omit if Node-only)
│   ├── app.py / main.py        # FastAPI entry
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── train.py                # Offline training script
│   ├── evaluate.py
│   ├── models/                 # Saved artifacts (.pkl, .onnx) — Git LFS, not raw git
│   └── tests/
├── supabase/
│   ├── config.toml
│   └── migrations/             # SQL migrations (source of truth for schema)
├── tests/
│   ├── unit/                   # Vitest / pytest
│   ├── integration/
│   └── e2e/                    # Playwright
├── .github/workflows/
│   ├── ci.yml                  # typecheck + lint + test + build
│   └── deploy.yml              # deploy on tag / main
├── Dockerfile                  # Web production image (if not Vercel-only)
├── docker-compose.yml          # Local: web + db + ml-service
├── next.config.mjs
├── tsconfig.json
├── tailwind.config.ts
├── playwright.config.ts
├── package.json
├── README.md
├── CONTRIBUTING.md
├── CHANGELOG.md
├── env.example
└── .gitignore
```

### 3.3 Purpose rules

- `app/` = routing only. No heavy logic in pages; move to `lib/` or backend.
- `app/api/` = thin: validate → auth → authorize → call service/DB/ML → return. Under ~100 lines per handler.
- `lib/` = pure, testable functions (no secrets, no direct DB unless server-only).
- `ml-service/` = stateless inference; no frontend imports from it.
- `supabase/migrations/` = only place schema changes happen.

**Checklist:**

- [ ] Layout matches above (or documented deviation)
- [ ] No business logic stranded in page components
- [ ] API handlers thin and consistently shaped

---

## 4. Environment Setup & Configuration

### 4.1 Twelve-factor config

- Code never contains secrets. All secrets come from environment.
- `NEXT_PUBLIC_*` is embedded in the browser bundle — **public by definition**. Never put service keys there.
- Server-only keys live in `process.env` on backend / ML service.

### 4.2 Required files

**`env.example` (commit this; never commit `.env.local`):**

```bash
# --- App ---
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# --- Database / Backend (Supabase example; swap for your provider) ---
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# --- Auth ---
AUTH_SECRET=generate-with-openssl-rand-hex-32

# --- Storage ---
S3_BUCKET=
S3_REGION=
S3_ACCESS_KEY_ID=
S3_SECRET_ACCESS_KEY=

# --- LLM providers (at least one for AI features) ---
OPENAI_API_KEY=
ANTHROPIC_API_KEY=
GROQ_API_KEY=
OPENROUTER_API_KEY=

# --- ML service (Option B) ---
ML_SERVICE_URL=http://localhost:8000
ML_SERVICE_TOKEN=shared-secret-for-web-to-ml-auth

# --- Observability ---
SENTRY_DSN=
```

**`.gitignore` essentials:**

```gitignore
node_modules/
.next/
out/
dist/
.env
.env.local
.env.*.local
*.pem
*.key
.vercel/
supabase/.branches/
supabase/.temp/
test-results/
playwright-report/
/coverage/
*.tsbuildinfo
__pycache__/
.venv/
ml-service/models/*.pkl
ml-service/models/*.onnx
```

### 4.3 Local tooling

| Tool | Purpose | Command |
|------|---------|---------|
| Node 20/22 + npm | Web runtime | `node -v; npm -v` |
| Python 3.10+ + venv | ML service | `python --version; pip --version` |
| Supabase CLI / Postgres | DB + Auth + Storage locally | `supabase start; supabase db push` |
| Docker + Compose | Reproducible local stack | `docker compose up --build` |

**Checklist:**

- [ ] `env.example` committed, `.env.local` ignored
- [ ] `NEXT_PUBLIC_*` contains no secrets (audit it)
- [ ] New dev can boot with README steps alone

---

## 5. Frontend Development

### 5.1 Principles

- **Server Components by default**, Client Components (`"use client"`) only for interactivity (forms, canvas, drag-drop, charts).
- **Route groups:** `(auth)` for public auth pages, `(app)` for protected shell. Middleware enforces, not just UI hiding.
- **Design system first:** build `components/ui/*` (Button, Input, Card, Dialog, Tabs, Progress) once, reuse everywhere. Use Tailwind tokens, not ad-hoc hex codes.

### 5.2 Data fetching pattern

```tsx
// Server Component: fetch on server, pass to client
// app/(app)/dashboard/page.tsx
import { createServerClient } from "@/utils/db/server";

export default async function DashboardPage() {
  const db = await createServerClient();
  const { data: items } = await db.from("items").select("*").order("created_at", { ascending: false });
  return <ItemList initial={items ?? []} />;
}
```

```tsx
// Client hook: mutations + refresh
// hooks/use-items.ts
"use client";
import { useCallback, useState } from "react";

export function useItems() {
  const [busy, setBusy] = useState(false);
  const create = useCallback(async (title: string) => {
    setBusy(true);
    try {
      const res = await fetch("/api/items", {
        method: "POST",
        headers: { "content-type": "application/json" },
        body: JSON.stringify({ title }),
      });
      if (!res.ok) throw new Error((await res.json()).error?.message ?? "Create failed");
      return await res.json();
    } finally { setBusy(false); }
  }, []);
  return { create, busy };
}
```

### 5.3 Forms, validation, UX states

- Validate twice: instant client feedback (Zod + `react-hook-form`) + authoritative server validation (same Zod schema in `lib/validation.ts`).
- Every async UI needs 4 states: `loading | empty | error | success`. No silent failures.
- File uploads: show progress, client pre-validation (type/size), server re-validation.

**Checklist:**

- [ ] Layouts: root + auth + app shell
- [ ] UI primitives done before feature pages
- [ ] All pages handle loading/empty/error
- [ ] No secrets or service keys in frontend code

---

## 6. Backend Development & API Design

### 6.1 REST conventions

```
GET    /api/items          → list (paginated ?page&limit)
POST   /api/items          → create {title}
GET    /api/items/:id      → read
PATCH  /api/items/:id      → update
DELETE /api/items/:id      → delete
POST   /api/uploads        → multipart upload
POST   /api/ml/predict     → {features} → {label, confidence}
POST   /api/ai/ask         → {question, scope} → {answer, citations}
GET    /api/health         → {ok:true, version, time}
```

Rules:

- JSON everywhere. Success: `{ data }` or resource directly. Error: `{ error: { code, message } }` with correct HTTP status (400 validation, 401 unauthenticated, 403 forbidden, 404 not found, 422 no evidence, 502 AI/ML upstream failed).
- Paginate lists (`?limit` capped, e.g. 50). Order deterministically.
- Version when breaking (`/api/v1/...`) — or state explicitly that v1 is unstable.

### 6.2 Handler template (Node / Next.js)

```ts
// app/api/items/route.ts
import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { getUserOr401 } from "@/lib/auth";
import { getServiceClient } from "@/utils/db/service";

const CreateSchema = z.object({ title: z.string().min(1).max(200) });

function err(code: string, message: string, status = 400) {
  return NextResponse.json({ error: { code, message } }, { status });
}

export async function POST(req: NextRequest) {
  const user = await getUserOr401();
  if (!user) return err("unauthorized", "Sign in required.", 401);

  const body = await req.json().catch(() => null);
  const parsed = CreateSchema.safeParse(body);
  if (!parsed.success) return err("bad_request", parsed.error.issues[0]?.message ?? "Invalid body.");

  const db = getServiceClient();
  const { data, error } = await db.from("items").insert({
    owner_id: user.id, title: parsed.data.title,
  }).select("id,title,created_at").single();

  if (error) return err("db_failed", error.message, 500);
  return NextResponse.json({ data }, { status: 201 });
}
```

### 6.3 Handler template (Python / FastAPI ML service)

```python
# ml-service/main.py
from fastapi import FastAPI, Header, HTTPException
from pydantic import BaseModel
import os

app = FastAPI()
TOKEN = os.environ.get("ML_SERVICE_TOKEN", "")

class PredictIn(BaseModel):
    text: str
    threshold: float = 0.5

class PredictOut(BaseModel):
    label: str
    confidence: float
    model_version: str

@app.get("/health")
def health():
    return {"ok": True, "model_version": "v1.0.0"}

@app.post("/predict", response_model=PredictOut)
def predict(body: PredictIn, authorization: str = Header(default="")):
    if authorization != f"Bearer {TOKEN}":
        raise HTTPException(status_code=401, detail="Unauthorized")
    # ... load model once at startup, preprocess, infer, return
    return PredictOut(label="positive", confidence=0.92, model_version="v1.0.0")
```

### 6.4 Backend rules

- Auth check first, ownership check second, validation third, work last.
- Service-role / privileged DB clients **only on server**. Never import them in client components (use `server-only` package to enforce).
- Timeouts: set `maxDuration` / timeouts for AI/ML routes (60–120s). Return `502` with actionable message on upstream failure; never leak stack traces or filesystem paths.
- Idempotency for ingest/jobs: safe to retry the same `resourceId` without duplicates.

**Checklist:**

- [ ] Consistent `{ data }` / `{ error: { code, message } }` envelope
- [ ] Auth + ownership on every mutating route
- [ ] Server validation with Zod/Pydantic (not just client)
- [ ] Health endpoint + version exposed

---

## 7. Database Design

### 7.1 Start relational (Postgres)

Typical v1 tables:

| Table | Purpose | Key columns |
|-------|---------|-------------|
| `profiles` | Per-user settings | `id (FK auth.users), display_name, created_at` |
| `projects` / `courses` | Top-level containers | `id, owner_id, title, status, created_at` |
| `modules` / `categories` | Subdivisions | `id, project_id, owner_id, position, title` |
| `items` / `resources` | Files, notes, records | `id, project_id, owner_id, storage_path, status` |
| `extractions` / `chunks` | ML-ready text pieces | `id, item_id, owner_id, seq, page_no, text` |
| `predictions` / `jobs` | Async ML work tracking | `id, item_id, owner_id, state, error, created_at` |
| `feedback` | Human corrections (retraining gold) | `id, prediction_id, correct_label` |

### 7.2 Migration discipline

- Migrations are the **only** way schema changes happen. Never click-to-edit prod schema.
- Each migration: `create table if not exists` + indexes + Row Level Security (RLS) policies.
- Example:

```sql
create table if not exists public.items (
  id uuid primary key default gen_random_uuid(),
  owner_id uuid not null references auth.users (id) on delete cascade,
  project_id uuid references public.projects (id) on delete set null,
  title text not null,
  created_at timestamptz not null default now()
);
create index if not exists items_owner_idx on public.items (owner_id, created_at desc);
alter table public.items enable row level security;
create policy "items_owner_all" on public.items
  for all using (auth.uid() = owner_id) with check (auth.uid() = owner_id);
```

### 7.3 Indexing & search progression

1. v1: B-tree on `(owner_id, created_at)`, GIN `to_tsvector('english', text)` for keyword search.
2. When semantic search needed: add `pgvector` extension + `embedding vector(1536)` + IVFFlat/HNSW index.
3. When scale demands: dedicated vector DB (Qdrant/Pinecone) — only after measuring.

**Checklist:**

- [ ] ER diagram (even hand-drawn) before coding schema
- [ ] All tables have `owner_id` + RLS
- [ ] Migrations reviewed, reversible, applied via CLI (`db push`)
- [ ] Seed script for local dev

---

## 8. Authentication & Authorization

### 8.1 Layers

1. **Authentication (who):** email/password + OAuth (Google/GitHub). Use a provider (Supabase Auth, Auth.js, Clerk) — do not hand-roll passwords.
2. **Authorization (what):** ownership checks (`owner_id = user.id`) in every query + RLS as defense-in-depth.
3. **Route protection:** middleware gates protected prefixes; guest-only pages redirect signed-in users.

```ts
// middleware.ts (conceptual)
const PROTECTED = ["/dashboard", "/projects", "/settings"];
const GUEST_ONLY = ["/signin", "/signup"];

export async function middleware(req: NextRequest) {
  const user = await getUser(req); // refresh session
  const path = req.nextUrl.pathname;
  if (isProtected(path) && !user) return redirect("/signin?next=" + path);
  if (isGuestOnly(path) && user) return redirect(safeNext(req) ?? "/dashboard");
  return NextResponse.next();
}
```

### 8.2 Rules

- Never trust client-provided `userId`. Derive from session server-side.
- Service-role keys bypass RLS — so app-layer ownership checks are load-bearing. Audit them.
- Validate redirect targets (`safeNext`: must start with `/` but not `//`) to prevent open redirects.

**Checklist:**

- [ ] Protected vs guest-only routes listed and tested
- [ ] Cross-user access returns 404 (not 403 with existence leak, pick one policy)
- [ ] Session refresh never 500s the whole app

---

## 9. File Uploads & Storage

### 9.1 Flow

```
[Browser] → POST /api/uploads (multipart, ≤ N files, validate ext/size)
  → Storage bucket (e.g. userId/projectId/scope/ts-rand-safeName)
  → DB row (storage_path, filename, mime, bytes, status: stored)
  → Async POST /api/process {resourceId} (extract → chunk → embed/classify)
```

### 9.2 Validation (both sides)

```ts
// lib/uploads.ts
export const MAX_FILE_MB = 50;
const ALLOWED = new Set(["pdf","docx","txt","md","csv","png","jpg","mp3","mp4","zip"]);
const BLOCKED = new Set(["exe","msi","bat","sh","ps1","apk","dmg"]);

export function validateFile(name: string, size: number): string | null {
  const ext = name.toLowerCase().split(".").pop() ?? "";
  if (!ext) return "File needs an extension.";
  if (BLOCKED.has(ext)) return `".${ext}" files are blocked for safety.`;
  if (!ALLOWED.has(ext)) return `".${ext}" is not supported yet.`;
  if (size <= 0) return "File is empty.";
  if (size > MAX_FILE_MB * 1024 * 1024) return `File exceeds ${MAX_FILE_MB} MB.`;
  return null;
}
```

- Sanitize filenames (`safeName`), namespace paths per user, cap files per request (e.g. 20), cap storage per user if needed.
- Process async: return upload receipt immediately, run extraction/classification in background or follow-up call. Track in `processing_jobs` table (`queued → processing → completed | failed`).

**Checklist:**

- [ ] Allow/block lists + size caps enforced client + server
- [ ] Storage paths namespaced, filenames sanitized
- [ ] Failed/empty extractions produce clear user message (e.g. scanned PDF)

---

## 10. Machine Learning Lifecycle (Classical ML / Deep Learning)

Use this for train-your-own models (classifier, regressor, recommender, vision, etc.). For LLM prompting without training, skip to §11 — but still read §10.5–10.7 on evaluation and serving.

### 10.1 Problem framing

| Task | Example | Baseline metric |
|------|---------|-----------------|
| Binary classification | Spam / not spam, syllabus / content | F1, AUC |
| Multiclass | Route ticket to team A/B/C | Macro-F1 |
| Regression | Predict delivery days, price | MAE / RMSE |
| Recommendation | Suggest next lesson | Recall@K, CTR |
| Clustering | Group similar documents | Silhouette (then human review) |

Start with a **heuristic baseline** (keyword rules, majority class) before any model. If the baseline is good enough, ship it.

### 10.2 Data pipeline

```
Raw (DB/Storage/CSVs)
  → Collect (SQL dump / ETL script, versioned with date + query)
  → Clean (dedupe, handle nulls, normalize text, strip PII if needed)
  → Label (human labels or weak rules; store in feedback table)
  → Split (train 70 / val 15 / test 15, stratified, time-based if temporal)
  → Version (DVC / date-stamped folder + data card README)
```

Rules:

- Never evaluate on training data. Lock a held-out test set early.
- Record dataset version + labeling rules alongside model version.
- Collect **feedback / corrections** from the app from day one (even if unused initially) — this becomes retraining gold.

### 10.3 Training (Python example)

```bash
python -m venv .venv && source .venv/bin/activate
pip install scikit-learn pandas numpy joblib fastapi uvicorn pytest
```

```python
# ml-service/train.py
import joblib
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# X: list[str], y: list[str] — loaded from versioned dataset
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, stratify=y, random_state=42)

pipe = Pipeline([
    ("tfidf", TfidfVectorizer(max_features=20000, ngram_range=(1,2))),
    ("clf", LogisticRegression(max_iter=1000)),
])
pipe.fit(X_train, y_train)
print(classification_report(y_test, pipe.predict(X_test)))
joblib.dump(pipe, "ml-service/models/text-clf-v1.pkl")
```

Track: model version (`v1`, date, git SHA), hyperparams, metrics, dataset hash. Tools: MLflow / Weights & Biases for experiments; start with a `METRICS.md` + saved artifacts if small.

### 10.4 Deep learning notes (when needed)

- Prefer **transfer learning** (pretrained ResNet / BERT / Whisper) + fine-tune over training from scratch.
- Separate `train.py` (GPU, offline) from `infer.py` (CPU-friendly, ONNX-quantized where possible).
- Save tokenizer + label map + preprocessing with the weights. Version them together.

### 10.5 Evaluation (minimum bar)

| Check | How |
|-------|-----|
| Offline metrics | Precision/recall/F1 (classification), MAE (regression) on held-out test |
| Slice analysis | Metrics per category / language / file type — catch hidden failures |
| Calibration | Is 0.9 confidence right 90% of the time? Use threshold tuning |
| Latency | p50/p95 inference time on CPU target; budget (e.g. < 300ms) |
| Ablation | Does each feature help? Remove and re-evaluate |
| Human review | Sample 50–100 predictions, label errors qualitatively |

Never ship on accuracy alone for imbalanced data. Report confusion matrix.

### 10.6 Serving patterns

| Pattern | When | How |
|---------|------|-----|
| **Synchronous API** | Fast models (< 1s) | FastAPI `POST /predict`, web app calls it, returns result inline |
| **Async job** | Slow (PDF OCR, video, large batches) | Enqueue → worker → `processing_jobs` → poll/webhook |
| **Edge / ONNX** | Tiny models, offline | Export ONNX, run in Node/Python without GPU |
| **Batch** | Nightly scoring | Cron job scores new rows, writes `predictions` table |

Web → ML auth: shared bearer token (`ML_SERVICE_TOKEN`), private network, timeouts + retries with backoff, circuit breaker if ML down (degrade gracefully, never 500 the page).

### 10.7 MLOps: retraining & monitoring

- Log every prediction: `input_hash, model_version, output, confidence, latency, timestamp`.
- Monitor: input drift (feature distributions), output drift (label ratios), confidence decay, latency, error rate.
- Retrain triggers: scheduled (monthly) + performance-based (F1 drop > X) + data-based (N new labeled samples).
- Keep previous model artifact for instant rollback. Blue/green or shadow deploy new versions.

**Checklist (§10):**

- [ ] Baseline heuristic measured before modeling
- [ ] Dataset versioned, test set locked
- [ ] Metrics + slices + latency recorded
- [ ] Model artifact versioned, rollback ready
- [ ] Inference behind authenticated API, not in frontend
- [ ] Prediction logging + drift monitoring planned

---

## 11. LLM & RAG Integration (Modern AI Features)

Use this for chat, summarization, Q&A over user documents, quiz/flashcard generation, note refinement.

### 11.1 Architecture: grounded generation

```
User question + scope (selection / page / project / course)
  → Retrieve evidence (keyword / vector search over owned chunks)
  → Build prompt: [SYSTEM rules] + [EVIDENCE with citations] + [QUESTION]
  → Provider chain (primary → fallback) → answer
  → If INSUFFICIENT → honest "not enough material" (never fabricate)
  → UI: answer + citation chips + Review-before-replace modal
```

### 11.2 Retrieval (RAG) — start simple

```ts
// 1. Chunk text (~800-1000 chars, paragraph boundaries, keep page refs)
// 2. Keyword retrieval (works without embeddings):
const terms = query.toLowerCase().split(/[^a-z0-9]+/).filter(t => t.length > 2).slice(0, 12);
const orFilter = terms.map(t => `text.ilike.%${t}%`).join(",");
// query chunks where owner + project match, score by term hits, top 6-8
// 3. Upgrade to pgvector embeddings when keyword fails on paraphrases
```

Chunk schema: `{ id, resource_id, project_id, owner_id, seq, page_no, text }`. Always scope by `owner_id + project_id`. Never mix users' data.

### 11.3 Provider routing with fallback

```ts
// lib/ml/prompts.ts concept
const CHAINS = {
  classify: ["primary-llm", "fallback-llm", "local-rules"],
  quiz: ["fast-llm", "primary-llm", "local-rules"],
  general: ["primary-llm", "fast-llm", "local-rules"],
};

export async function chatFor(job: Job, messages: Msg[]) {
  for (const provider of CHAINS[job]) {
    if (!isConfigured(provider)) continue;
    try { return await callProvider(provider, messages); }
    catch (e) { errors.push(`${provider}: ${e.message}`); continue; }
  }
  throw new Error(errors.length ? `AI unavailable: ${errors.join("; ")}` : "No AI provider configured.");
}
```

- Keys read at call time, never logged. Choose per-job primary (e.g. instruction-following model for classification, fast model for quiz).
- Parse defensively: strip ``` fences, extract first JSON object/array (`parseJsonResult`), validate with Zod before saving.

### 11.4 Prompt patterns

**Grounded Q&A system prompt:**

```
Answer ONLY from the provided sources. Cite claims as [1], [2] matching source order.
If sources don't support the answer, say INSUFFICIENT and stop.
Never invent page numbers, facts, or citations. Keep answers concise.
```

**Refine modes (never silent overwrite):**

| Mode | Instruction |
|------|-------------|
| simplify | Rewrite in plainer language, same meaning, shorter sentences |
| grammar | Fix grammar/spelling only, keep wording identical |
| organize | Restructure into headings + bullets, keep every fact |
| improve | Improve clarity/flow of answer, keep facts accurate |
| exam | Concise key points + definitions + likely question phrasing |

Every AI edit → **Review modal** (Original vs Proposed → Accept / Insert as new / Cancel).

### 11.5 Evals for LLM features

- Golden set: 20–50 Q&A pairs with known answers + citations. Run on every prompt/model change.
- Metrics: grounded rate (% answers with valid citations), abstention precision (says INSUFFICIENT when it should), latency, cost per 1k requests.
- Red-team: cross-user leakage (can user A retrieve user B's chunks?), prompt injection in uploads (treat stored text as evidence, never instructions).

**Checklist (§11):**

- [ ] Retrieval scoped by owner + project, top-K cited
- [ ] INSUFFICIENT / no-provider paths return calm messages
- [ ] Provider fallback chain + per-job routing
- [ ] No silent overwrites (review modal)
- [ ] Golden eval set + leakage test

---

## 12. Build System & Scripts

### 12.1 Node scripts (`package.json`)

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "typecheck": "tsc --noEmit",
    "lint": "eslint . --max-warnings=0",
    "format": "prettier --write .",
    "test": "vitest run",
    "test:e2e": "playwright test",
    "db:push": "supabase db push",
    "db:reset": "supabase db reset"
  }
}
```

### 12.2 Python scripts (`ml-service/`)

```bash
pip install -r requirements.txt
python train.py --data data/v1 --out models/v2
python evaluate.py --model models/v2 --test data/test
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
pytest
```

**Checklist:**

- [ ] `dev`, `build`, `start`, `typecheck`, `lint`, `test`, `test:e2e` all work from clean clone
- [ ] README documents each script's purpose

---

## 13. Dependency Management

| Ecosystem | Manager | Lock file | Install (CI) |
|-----------|---------|------------|--------------|
| Node | npm (or pnpm/yarn — pick one) | `package-lock.json` | `npm ci` |
| Python | pip + venv (or Poetry/uv) | `requirements.txt` + hashes / `poetry.lock` | `pip install -r requirements.txt` |

Rules:

- Commit lock files. CI uses `ci` (frozen), never `install` (floating).
- Pin majors for frameworks (`next`, `react`, `fastapi`), allow patches via `^`/`~=`.
- Audit monthly: `npm audit`, `pip audit`, Dependabot/Renovate enabled.
- Justify heavy deps (ML libs are large — keep training deps out of inference image via multi-stage Docker).

---

## 14. Testing Strategy

### 14.1 Pyramid

```
      /\  E2E (Playwright, ~5-20 tests: critical paths)
     /--\  Integration (API + DB, ~20-50: auth, CRUD, ML proxy)
    /----\  Unit (vitest/pytest, 100s: pure functions, chunking, scoring, prompts)
```

### 14.2 What to test (with examples)

**Unit (fast, no network):**

- Text chunking, syllabus parsing, filename validation, scoring/ranking, prompt builders, doc coercion.
- ML: preprocessing, feature shapes, threshold logic, metric computation.

```ts
// tests/unit/chunk.test.ts
import { describe, expect, it } from "vitest";
import { chunkText } from "@/lib/chunk";

describe("chunkText", () => {
  it("splits on paragraphs under limit", () => {
    const chunks = chunkText("a\n\nb\n\nc", null);
    expect(chunks.length).toBeGreaterThan(0);
  });
});
```

**Integration (API + DB):**

- 401 without session, 404 for foreign-owned resource, 422 for invalid body, happy-path create→read→delete.
- ML proxy: mock ML service, assert web handles 502 gracefully.

**E2E (Playwright, prod build):**

```ts
// tests/e2e/app.spec.ts
import { test, expect } from "@playwright/test";

test("landing renders", async ({ page }) => {
  await page.goto("/");
  await expect(page.getByRole("heading").first()).toBeVisible();
});

test("dashboard redirects when signed out", async ({ page }) => {
  await page.goto("/dashboard");
  await expect(page).toHaveURL(/\/signin/);
});
```

**ML-specific:**

- Offline eval script in CI (fail if F1 drops > threshold vs baseline).
- Golden LLM set (see §11.5). Data validation (schema, null rates, label distribution) on dataset updates.

### 14.3 Config

- `playwright.config.ts`: `testDir: ./tests/e2e`, `baseURL`, 1–3 browsers, `webServer` boots prod (`npm run start -- -p 3100`), `retries: 2` on CI.
- Coverage: `vitest --coverage`, `pytest --cov`. Gate new code (e.g. 80% on `lib/`), don't chase 100% globally.

**Checklist:**

- [ ] Unit + integration + e2e all present (even small)
- [ ] Auth boundaries tested (401/404)
- [ ] ML eval / golden set in CI
- [ ] No tests hitting external sites (no `playwright.dev` boilerplate)

---

## 15. Code Quality & Conventions

| Tool | Type | Config |
|------|------|--------|
| TypeScript `strict` | Type safety | `tsconfig.json` (`strict:true`, `noEmit`, `jsx:preserve`) |
| ESLint | Lint | `.eslintrc` / `eslint.config.mjs` |
| Prettier (or Biome) | Format | `.prettierrc` |
| `server-only` | Boundary guard | `import "server-only"` in server libs |
| Zod / Pydantic | Validation | Schemas in `lib/validation.ts` / `schemas.py` |
| Husky + lint-staged | Pre-commit | typecheck + lint + format on staged files |
| Sonar / CodeQL (optional) | SAST | GitHub Advanced Security |

Conventions:

- Paths: `@/*` alias. Files: `kebab-case.ts`, components `PascalCase.tsx`.
- Functions: small, pure where possible, named verbs (`parseX`, `scoreY`, `buildZ`).
- Errors: user-friendly messages, no stack/paths leaked. Log full error server-side with request ID.
- Comments: explain *why*, not *what*. Document workarounds with link + date.

**Checklist:**

- [ ] `typecheck` + `lint` clean, enforced in CI + pre-commit
- [ ] Formatter applied (no style debates in PRs)
- [ ] No `any` / bare `except:` without justification

---

## 16. CI/CD Pipeline

### 16.1 Minimal production CI (`.github/workflows/ci.yml`)

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  web:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci
      - run: npm run typecheck
      - run: npm run lint
      - run: npm run test -- --run
      - run: npm run build
      - run: npx playwright install --with-deps
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with: { name: playwright-report, path: playwright-report/, retention-days: 14 }

  ml:
    runs-on: ubuntu-latest
    defaults: { run: { working-directory: ml-service } }
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.11", cache: pip }
      - run: pip install -r requirements.txt
      - run: pytest
      - run: python evaluate.py --check-threshold  # fail if below baseline
```

### 16.2 CD (deploy on tag or main)

- Web → Vercel / Docker push; ML service → Render/Fly/Cloud Run; DB via `supabase db push` (manual approval for prod).
- Tag releases: `v1.2.0` → GitHub Release with changelog. Never auto-migrate prod without backup.

**Checklist:**

- [ ] CI runs typecheck + lint + unit + build + e2e + ML eval
- [ ] npm/pip caching enabled, pinned versions
- [ ] Deploy requires green CI + human approval for prod DB

---

## 17. Docker & Containerization

### 17.1 Web `Dockerfile` (production)

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["npm", "run", "start", "--", "-p", "3000"]
```

### 17.2 ML `Dockerfile` (slim inference)

```dockerfile
FROM python:3.11-slim
WORKDIR /srv
COPY requirements-infer.txt ./
RUN pip install --no-cache-dir -r requirements-infer.txt
COPY main.py infer.py models/text-clf-v1.pkl ./
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 17.3 `docker-compose.yml` (local)

```yaml
services:
  web:
    build: .
    ports: ["3000:3000"]
    env_file: .env.local
    depends_on: [ml, db]
  ml:
    build: ./ml-service
    ports: ["8000:8000"]
    env_file: .env.local
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: postgres
    ports: ["5432:5432"]
    volumes: [pgdata:/var/lib/postgresql/data]
volumes:
  pgdata:
```

**Checklist:**

- [ ] `docker compose up --build` boots web + ml + db locally
- [ ] Training deps excluded from inference image
- [ ] Model artifacts via volume / registry / LFS, not baked secrets

---

## 18. Deployment

| Target | Good for | Notes |
|--------|----------|-------|
| Vercel | Next.js web | Zero-config, previews per PR; set env in dashboard; add Auth redirect URLs |
| Render / Fly / Cloud Run | Python ML service, Docker web | Autoscale, private networking web→ml |
| Supabase / Neon / RDS | Postgres | Backups on; `db push` via CLI; pooler for serverless |
| S3 / Supabase Storage | Files | Public vs private buckets; CDN for public assets |
| Hugging Face Inference / Replicate | Hosted ML | Skip self-hosting for standard models |

Env per environment (dev / staging / prod): separate projects/keys, never share prod service keys with previews. Document required vars in `env.example` + hosting dashboard.

**Checklist:**

- [ ] Staging mirrors prod (separate DB/buckets/keys)
- [ ] Auth redirect URLs include prod + staging
- [ ] Backups + restore tested (DB + storage)
- [ ] Rollback plan (previous image / model artifact)

---

## 19. Observability, Logging & Monitoring

| Layer | Tool examples | What to capture |
|-------|---------------|-----------------|
| Errors | Sentry | Exceptions with request ID, user ID (hashed), route, model version |
| Logs | Pino / structlog → stdout | `req_id, user_id, route, latency_ms, outcome, model_version, provider` |
| Metrics | Prometheus / Grafana / hosting dashboards | Request rate, p50/p95 latency, 5xx rate, ML confidence histogram, GPU util |
| Uptime | BetterStack / Checkly | `/api/health` every minute |
| ML drift | Custom job + Evidently / manual | Input/output distributions, abstention rate, feedback rate |

Rules:

- One request ID across web → ML → DB logs.
- Never log prompts with PII, keys, or full documents. Log hashes + lengths + IDs.
- Alert on: 5xx spike, p95 latency breach, ML error rate, disk/quota, failed jobs growth.

**Checklist:**

- [ ] `/api/health` + uptime check
- [ ] Error tracking with source maps
- [ ] Prediction + latency logging (ML)
- [ ] Alerts with owner + runbook link

---

## 20. Security Checklist

- [ ] Secrets only in env / secret manager; `.env*` ignored; no keys in git history (scan with gitleaks)
- [ ] `NEXT_PUBLIC_*` audited — no private keys
- [ ] Auth on all mutating routes; ownership checks server-side; RLS enabled
- [ ] Validation server-side (Zod/Pydantic); file type/size caps both sides
- [ ] Uploads: sanitize names, namespace paths, scan if executable risk; serve with correct `content-type`
- [ ] Rate limiting on auth, uploads, AI/ML endpoints (e.g. Upstash / middleware)
- [ ] CORS locked to app origins; CSP headers; `X-Content-Type-Options: nosniff`
- [ ] Dependency audit (`npm audit`, `pip audit`) + Dependabot
- [ ] Backups encrypted; PII minimization + deletion path (GDPR-style `DELETE /api/account`)
- [ ] LLM: stored docs treated as evidence, never instructions; output sanitized (no raw HTML/JS injection)

---

## 21. Performance & Scaling

- **Frontend:** Server Components, image optimization (`next/image`), code-splitting, `loading.tsx` skeletons, debounce autosaves, virtualize long lists.
- **Backend:** Paginate, index (`owner_id, created_at`), `select` only needed columns, cache hot reads (ISR / Redis), queue slow work.
- **ML:** Batch inference, cache embeddings, quantize (ONNX/int8), GPU only where measured needed; set timeouts + fallbacks (keyword rules when model down).
- **DB:** Connection pooling (PgBouncer/Supabase pooler), `EXPLAIN ANALYZE` slow queries, archive cold data.
- Budgets: TTFB < 500ms (cached), API p95 < 800ms (non-AI), AI p95 < 15s with streaming where possible, Lighthouse ≥ 90.

**Checklist:**

- [ ] Budgets defined and measured (Lighthouse + APM)
- [ ] N+1 queries eliminated, indexes verified
- [ ] Slow ML/AI paths async with progress UI

---

## 22. Documentation

| Doc | Contents | Status |
|-----|----------|--------|
| `README.md` | What/why, demo GIF, stack, quickstart, env table, scripts, deploy, license | Required |
| `CONTRIBUTING.md` | Branching, commits (Conventional), PR template, local boot, code style | Required for teams |
| `CHANGELOG.md` | Semver releases, breaking changes | Required after v1 |
| `docs/architecture.md` | Diagrams (request flow, RAG flow, ERD), ADRs | Recommended |
| `docs/api.md` | All endpoints: method/path/auth/body/statuses/examples | Recommended |
| `docs/ml.md` | Dataset card, metrics, model registry, eval procedure, rollback | Required if ML |
| `docs/runbook.md` | On-call: alerts, common incidents, rollback, contacts | Required for prod |

README quickstart template:

```markdown
## Quickstart
\`\`\`bash
cp env.example .env.local   # fill keys (see table below)
npm ci
npm run db:push
npm run dev                 # http://localhost:3000
# ML service (if applicable):
cd ml-service && python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt && uvicorn main:app --reload
\`\`\`
```

**Checklist:**

- [ ] New hire boots from README alone (test it)
- [ ] API + ML docs match code (review each release)
- [ ] ADRs for major decisions (why Postgres? why RAG vs fine-tune?)

---

## 23. Step-by-Step: Build From Scratch

### Phase 0 — Plan (Day 1)

1. Write 1-page scope (§1.2). List 5 user stories + 3 non-goals.
2. Draw boxes: pages, API routes, tables, ML inputs/outputs. Photograph whiteboard into `docs/`.
3. Choose stack (§2), create repo, push empty README + `.gitignore` + `env.example`.

### Phase 1 — Skeleton (Days 2–3)

```bash
npx create-next-app@latest . --typescript --tailwind --app --src-dir=false
npm install zod server-only
npx tsc --noEmit   # green before features
```

4. Add `app/layout.tsx`, `app/page.tsx` (landing), `app/(app)/layout.tsx` (shell), `middleware.ts` (guest → signin).
5. Add `components/ui/*` (Button, Input, Card, Dialog) + `globals.css` tokens.
6. Add `/api/health` + Playwright smoke (landing + redirect). CI green.

### Phase 2 — Data + Auth (Days 4–6)

7. Design tables (§7), write `supabase/migrations/0001_core.sql`, `db push`.
8. Wire Auth (signup/signin/callback), protect `(app)` routes, test 401/404.
9. Build first CRUD (e.g. projects/items) end-to-end: page → API → DB → list/detail.

### Phase 3 — Files & Processing (Days 7–9)

10. `POST /api/uploads` (validate → Storage → DB row) + `hooks/use-uploads` with progress.
11. `POST /api/process` (extract → chunk → save `chunks`) + `processing_jobs` tracking.
12. Verify: upload PDF → chunks visible → keyword search returns it.

### Phase 4 — ML v1 (Days 10–14)

**Classical path:**

13. Export dataset, train baseline + model (`ml-service/train.py`), record metrics.
14. Serve via FastAPI (`/predict`), proxy from `POST /api/ml/predict`, show result in UI.
15. Add `feedback` button (correct/incorrect) → store for retraining.

**LLM/RAG path (can parallelize):**

13. Implement `retrieve()` (keyword first) + `POST /api/ai/ask` (grounded prompt + citations + INSUFFICIENT).
14. Add 1–2 refine modes + Review modal. Golden eval set (20 Q&As), leakage test.
15. Provider fallback chain + per-job routing. Log latency/cost.

### Phase 5 — Harden (Days 15–18)

16. Unit + integration + e2e to cover critical paths + auth boundaries.
17. ESLint/Prettier/Husky, `typecheck` in CI, `npm audit` clean.
18. Rate limits, CSP, error tracking (Sentry), `/api/health` monitoring.
19. Dockerfiles + `docker-compose up` green. Staging deploy + restore test.

### Phase 6 — Launch (Day 19+)

20. Tag `v1.0.0`, write CHANGELOG, run go-live checklist (Appendix C).
21. Announce, measure (§1 success metrics), collect feedback → prioritize retraining / next features.

**Checklist:**

- [ ] Each phase ends with a demoable, deployable increment
- [ ] No phase starts without prior phase green in CI

---

## 24. Common Pitfalls & Recommendations

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Secrets in frontend | Keys in `NEXT_PUBLIC_*`, strange billing | Audit env; route all provider calls via backend |
| No ownership checks | User A reads user B data | Server `owner_id` checks + RLS + cross-user tests |
| God components/handlers | 1000-line page/route | Split: pages route, `lib/` computes, API thin |
| Training-serving skew | Great offline, bad live | Same preprocessing code path; log + compare distributions |
| Fabricating AI | Confident wrong answers, fake citations | Grounded prompts + INSUFFICIENT + citation chips + evals |
| Untested happy path | Breaks on demo | Playwright smoke on landing + core loop + auth |
| Binary bloat in git | Slow clone, huge repo | Git LFS for models/media; ignore build outputs; one skills/vendor source |
| Missing env docs | "Works on my machine" | `env.example` + README table + staging parity |
| No rollback | Bad deploy = outage | Version models + images; keep last-good artifact; practice restore |
| Premature vector DB | Cost/complexity without gain | Start keyword + pgvector; graduate when measured |

---

## Appendix A: Commands Cheat Sheet

```bash
# --- Git ---
git init; git add -A; git commit -m "feat: initial skeleton"
git log --oneline | wc -l
git shortlog -s -n --all
git log --pretty=format: --name-only | sort | uniq -c | sort -rn | head -20

# --- Node ---
node -v; npm -v
npm ci
npm run dev            # http://localhost:3000
npm run typecheck      # tsc --noEmit
npm run lint
npm run build && npm run start -- -p 3100
npx playwright install --with-deps && npx playwright test

# --- Python ML ---
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r ml-service/requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
pytest ml-service/tests -q
python ml-service/train.py --data data/v1 --out ml-service/models/v2
python ml-service/evaluate.py --model ml-service/models/v2 --test data/test

# --- Database (Supabase example) ---
supabase start
supabase db push
supabase db reset
supabase gen types typescript --local > lib/database.types.ts

# --- Docker ---
docker compose up --build
docker build -t web:latest .
docker build -t ml:latest ./ml-service

# --- Quality ---
npx tsc --noEmit
npx eslint . --max-warnings=0
npm audit --audit-level=moderate
pip audit
```

---

## Appendix B: Templates

### B.1 `package.json` scripts (copy-paste)

```json
{
  "name": "fullstack-ml-starter",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "typecheck": "tsc --noEmit",
    "lint": "eslint . --max-warnings=0",
    "format": "prettier --write .",
    "test": "vitest run",
    "test:e2e": "playwright test",
    "db:push": "supabase db push"
  }
}
```

### B.2 API error envelope

```ts
function err(code: string, message: string, status = 400) {
  return NextResponse.json({ error: { code, message } }, { status });
}
// 401 unauthorized, 403 forbidden, 404 not_found,
// 422 no_evidence / validation, 502 ai_failed / ml_failed
```

### B.3 Model card (`ml-service/models/MODEL_CARD.md`)

```markdown
# Model: text-clf-v1
- Task: binary text classification
- Dataset: data/v1 (hash: <sha>, N=12,400, split 70/15/15 stratified)
- Metrics: P=0.91 R=0.88 F1=0.895 AUC=0.96 (test)
- Latency: p95 180ms CPU
- Limitations: short texts < 10 chars, non-English degrades
- Rollback: models/text-clf-v0.pkl
```

### B.4 PR template (`.github/pull_request_template.md`)

```markdown
## What / Why
## How tested (unit/integration/e2e/eval)
## Screenshots / latency / metrics
## Risk + rollback
## Docs updated? (README/API/ML card/CHANGELOG)
```

---

## Appendix C: Go-Live Checklist

- [ ] Scope + success metrics written; non-goals explicit
- [ ] `env.example` + README quickstart verified from clean clone
- [ ] Auth: signup/signin/OAuth + protected/guest-only + 401/404 tests
- [ ] CRUD + uploads + processing end-to-end on staging
- [ ] ML: metrics recorded, threshold set, fallback works with provider/ML down
- [ ] LLM (if any): grounded, cited, INSUFFICIENT path, no silent overwrites, leakage test passed
- [ ] Tests: unit + integration + e2e green; coverage on critical paths
- [ ] Quality: typecheck + lint + format + audit clean
- [ ] Security: secrets audit, RLS, rate limits, CSP, CORS, backups
- [ ] Deploy: staging mirrors prod, health + uptime + errors wired, rollback practiced
- [ ] Docs: README, CONTRIBUTING, CHANGELOG, API, ML card, runbook
- [ ] Tag `v1.0.0`, release notes, announce, measure

---

## 25. AI Mistakes While Developing Software (100 Catalog)

> This section is a field guide for using AI coding assistants safely in a full-stack + ML project. AI accelerates scaffolding and boilerplate but lacks true semantic understanding, runtime context, version awareness, and security judgment. Every AI suggestion must be verified: typecheck it, test it, review it, and run it.

### 25.1 Why AI makes these mistakes

| Root cause | What happens | Example |
|------------|--------------|---------|
| Next-token prediction, not execution | Generates plausible-looking code that never ran | Invented method `Array.remove()` |
| Training cutoff + version drift | Suggests deprecated APIs, old configs | `request` library, Node 14 Dockerfile for Node 18 code |
| No repo-wide context | Misses cross-file contracts, env, ownership | Hardcoded `localhost:3000`, missing imports |
| No runtime / infra awareness | Ignores latency, state, scaling, timeouts | N+1 queries, in-memory cache in serverless |
| Security-blind completion | Optimizes for brevity, skips guards | String-concatenated SQL, `verify=False`, `*` CORS |
| Overconfidence + verbosity | States wrong facts authoritatively | Hallucinated cloud service `AWS CloudTable`, fake npm package |
| Placeholder merging | Deletes logic with `// ... rest of code` | Blind code merge losing business rules |

**Golden rule:** AI proposes, humans dispose. Treat AI output as an untrusted junior PR: never merge without `typecheck + tests + manual review + staging run`.

### 25.2 How to use this catalog

- Each mistake below has: **What → Why AI does it → Wrong vs Right → How to catch**.
- Cross-links to this guide: e.g. pagination (§6), RLS (§7–§8), secrets (§4, §20), Docker (§17), testing (§14).
- Copy Appendix D as your PR checklist for any AI-generated code.

---

### A. Architecture and System Design Mistakes (1–25)

#### 1. Hallucinating cloud services
**What:** Invents services (`AWS CloudTable`) or blends two names.
**Why:** Pattern-matches service names without a live catalog.
**Wrong:** `aws cloudtable create-table ...`
**Right:** Verify against provider docs; pin to known services (S3, RDS, SQS).
**Catch:** `grep` docs link required in PR; deploy to staging; IaC `plan` fails fast.

#### 2. Ignoring horizontal scalability (stateful services)
**What:** Session in local memory; breaks with 2+ replicas.
**Why:** Local examples dominate training data.
**Right:** Externalize state: Redis / DB / sticky-free JWT + server session store.
```ts
// wrong: const sessions = new Map()
// right: await redis.set(`sess:${id}`, JSON.stringify(s), { EX: 3600 })
```
**Catch:** Load test with 2 replicas; review for in-process `Map` in backend.

#### 3. Over-engineering simple CRUD apps
**What:** Kubernetes + event bus for an internal tool needing monolith + SQLite/Postgres.
**Why:** Associates "production" with complex buzzwords.
**Right:** Start monolith (§2–§3); extract services only on measured need.
**Catch:** ADR required for any queue/orchestrator; ask "what breaks without it?".

#### 4. Database schema denormalization
**What:** Blind flattening → duplication, update anomalies.
**Why:** Optimizes for read example without write constraints.
**Right:** Normalize first (3NF), denormalize deliberately with sync jobs + docs.
**Catch:** Schema review; check update path for every duplicated field.

#### 5. Missing database indexes / keys
**What:** `CREATE TABLE` without PK/FK/indexes on queried columns.
**Why:** Omits "boring" DDL for brevity.
**Right:** Every table: PK, `owner_id` index, FK indexes; `EXPLAIN ANALYZE` slow queries (§7).
**Catch:** Migration linter; `EXPLAIN` in PR for new queries.

#### 6. Synchronous bias for long jobs
**What:** HTTP request waits for video/PDF/ML training.
**Why:** Simple request/response examples dominate.
**Right:** Async: enqueue → worker → `processing_jobs` → poll/webhook (§6.4, §10.6).
**Catch:** Timeout test; any handler > 5s must be async.

#### 7. Ignoring rate limits / backoff
**What:** `while(true) fetch()` without 429 handling.
**Why:** Happy-path snippets omit resilience.
**Right:** Exponential backoff + jitter + `Retry-After` respect + circuit breaker.
**Catch:** Chaos test with mocked 429; review loops calling network.

#### 8. Inappropriate tech-stack pairing
**What:** React inside Jinja without API bridge; ORM misuse across stacks.
**Why:** Mixes snippets from different ecosystems.
**Right:** Clear boundary (§2): web ↔ API (JSON) ↔ ML service; no cross-render hacks.
**Catch:** Architecture diagram check; `grep` for cross-stack imports.

#### 9. State mutation in React
**What:** `state.count = 1` instead of setter.
**Why:** Copies mutable patterns from other languages.
```tsx
// wrong: state.items.push(x)
// right: setItems(prev => [...prev, x])
```
**Catch:** ESLint `react-hooks` + immutable review; test re-render.

#### 10. Circular dependencies
**What:** A imports B, B imports A → init crash.
**Why:** No module-graph awareness.
**Right:** Extract shared types/utils to `lib/` (§3); dependency-cruiser in CI.
**Catch:** `madge --circular`, typecheck + startup smoke.

#### 11. Hardcoding configuration
**What:** DB URIs/keys inline.
**Why:** Examples inline values for clarity.
**Right:** Env only (§4); `env.example` + runtime validation with Zod.
**Catch:** `gitleaks` + `grep -r "postgres://\|sk-\|AKIA"` in CI.

#### 12. Monolithic data store for microservices
**What:** N services, one shared schema → coupling.
**Why:** Simplifies example.
**Right:** One service owns its tables; cross-service via API/events, not shared tables.
**Catch:** Ownership column in ERD; forbid cross-service direct table access.

#### 13. Ignoring pagination
**What:** `SELECT * FROM users` / `GET /items` unbounded.
**Why:** Small-dataset examples.
**Right:** `?limit` (cap 50) + `?cursor/page` + total/count headers (§6.1).
**Catch:** Load test with 100k rows; review every list endpoint.

#### 14. Lack of idempotency
**What:** Retry double-charges / double-creates.
**Why:** Omits failure-retry thinking.
**Right:** Idempotency keys (`Idempotency-Key` header → dedupe table) for payments/webhooks.
**Catch:** Retry test (send twice, assert one effect).

#### 15. Overlooking timezones
**What:** Local server time stored; global users see wrong order.
**Why:** Naive `new Date()` examples.
**Right:** Store UTC (`timestamptz`), convert at display; tests across zones.
**Catch:** `grep` for timezone-naive storage; test with TZ=Pacific/Kiritimati.

#### 16. In-memory caching in serverless
**What:** `dict` cache in Lambda/edge → lost on cold start.
**Why:** Serverful assumptions.
**Right:** External cache (Redis/Upstash) or revalidate pattern.
**Catch:** Two-invocation test; review for module-level `Map`.

#### 17. Ignoring network latency (N+1)
**What:** Loop with per-row query.
**Why:** Readable line-by-line logic.
```sql
-- wrong: 1000 x SELECT * FROM chunks WHERE id=...
-- right: SELECT * FROM chunks WHERE id = ANY($1)
```
**Catch:** Query-count assertion; APM / log query count per request.

#### 18. Poor error boundaries
**What:** No global handler; one exception kills process.
**Why:** Snippets omit scaffolding.
**Right:** Global `try/catch` + process handlers + framework error boundary + Sentry (§19).
**Catch:** Kill-test (throw in handler, assert process alive + 500 JSON, not crash).

#### 19. Improper threading (GIL)
**What:** Python `threading` for CPU-bound ML.
**Why:** Copies I/O-threading advice to CPU work.
**Right:** `multiprocessing` / job queue / GPU batch / Node worker threads where apt.
**Catch:** CPU profile; if threads don't speed up, switch model.

#### 20. Bloated JWT payloads
**What:** Entire profile in JWT → header overflow.
**Why:** Convenience over protocol limits (~8KB headers).
**Right:** JWT = `{sub, exp, role}`; fetch profile from DB.
**Catch:** Size assertion (< 2KB); review `sign()` payload.

#### 21. Assuming perfect networks (no timeouts)
**What:** `fetch(url)` without timeout → hang forever.
**Why:** Examples omit resilience for brevity.
**Right:** `AbortController.timeout(8000)` + retries + fallback (§6.4).
**Catch:** Fault-injection (blackhole endpoint, assert timeout).

#### 22. Misunderstanding CAP theorem
**What:** Claims consistency + availability during partition.
**Why:** Repeats marketing slogans.
**Right:** Document tradeoff per feature (CP for payments, AP for feed) in ADR.
**Catch:** Design review question: "what happens on partition?".

#### 23. Ignoring data retention / TTL
**What:** Logs/audit grow forever → disk full.
**Why:** No lifecycle thinking.
**Right:** TTL / partitioning / cron purge + archival to cold storage.
**Catch:** Growth dashboard; retention policy per table in migration comment.

#### 24. Failing to sanitize file uploads (arch)
**What:** Save uploads to app filesystem, executable path.
**Why:** Local-dev simplicity.
**Right:** Isolated object store (§9), random keys, content-type validation, no exec bit.
**Catch:** Upload `.exe/.html` test; verify stored outside webroot.

#### 25. Reinventing the wheel (crypto/dates)
**What:** Custom hash / date parser.
**Why:** Generates from scratch vs recalling lib.
**Right:** `bcrypt/argon2`, `date-fns/dayjs`, `zod`, battle-tested libs (§13).
**Catch:** `grep` for `md5(` / hand-rolled `hash(`; require lib justification.

---

### B. Code Generation and Logic Errors (26–50)

#### 26. Deprecated API usage
**What:** `request` lib, old SDK signatures.
**Why:** Cutoff + popular old posts.
**Right:** Check docs date; `npm view <pkg> version`; prefer `fetch`/`axios` current.
**Catch:** `npm outdated` + docs-link in PR.

#### 27. Off-by-one errors
**What:** Skips last element / OOB.
**Right:** Unit tests with 0/1/N boundaries (§14); prefer `.map/.slice` over manual indices.
**Catch:** Boundary tests mandatory for loops/slicing.

#### 28. Incorrect variable scoping (`var` in async loops)
**What:** Closure captures last value.
```js
// wrong: for (var i=0;...) setTimeout(()=>console.log(i))
// right: for (let i=0;...) ...
```
**Catch:** ESLint `no-var`; async-loop test.

#### 29. Silent failure swallowing
**What:** `try: ... except: pass`.
**Why:** Makes example "work".
**Right:** Log with context + request ID; rethrow or return typed error.
**Catch:** `grep -r "except.*pass\|catch.*{}"` fails CI.

#### 30. Memory leaks in event listeners
**What:** `addEventListener` without cleanup on unmount.
**Right:** `useEffect return () => removeEventListener` + test mount/unmount.
**Catch:** Heap snapshot / repeated mount test.

#### 31. Regex catastrophes (ReDoS)
**What:** `(a+)+$` freezes CPU.
**Why:** Clever-looking patterns untested.
**Right:** Simple regex + length cap + timeout; use validator libs.
**Catch:** Fuzz with long input; `eslint-plugin-security` / review.

#### 32. Type / method hallucination
**What:** `Array.remove()`, fake SDK methods.
**Why:** Plausible-name invention.
**Right:** Typecheck (`tsc --noEmit`) + IDE hover + docs check.
**Catch:** CI typecheck blocks merge (see §16).

#### 33. Floating-point math
**What:** `0.1+0.2==0.3` false; money errors.
**Right:** Integer cents / `Decimal`; epsilon compare for floats.
**Catch:** Money tests in cents; `grep` for float price math.

#### 34. Race conditions
**What:** Concurrent writes without lock/atomic.
**Why:** Sequential mental model.
**Right:** DB transactions, `SELECT ... FOR UPDATE`, queues, idempotency (§6).
**Catch:** Parallel test (Promise.all writes, assert one winner).

#### 35. Infinite `useEffect` loops
**What:** Effect sets state it depends on.
**Right:** Correct deps array + guard + `useCallback`; ESLint `exhaustive-deps`.
**Catch:** Render-count test; lint blocks.

#### 36. Incorrect null checks
**What:** `=== null` misses `undefined`.
**Right:** `== null` (covers both) or explicit `=== null || === undefined`; Zod defaults.
**Catch:** Tests with missing vs null fields.

#### 37. Pointer / lifetime errors (C/C++)
**What:** Return stack pointer → segfault.
**Right:** Heap / smart pointers / out-params; ASan in CI if native code.
**Catch:** ASan + review for `return &local`.

#### 38. Unoptimized recursion
**What:** Deep recursion → stack overflow.
**Right:** Iterative + memo; tail-call not relied upon in JS/Python.
**Catch:** Depth-10k test; prefer loop.

#### 39. Incomplete `switch` (fallthrough)
**What:** Missing `break` runs next case.
**Right:** `break/return` every case + `default`; prefer object map / exhaustive TS check.
**Catch:** ESLint `no-fallthrough`.

#### 40. Variable shadowing
**What:** Local same name as outer → wrong value.
**Right:** Distinct names; `no-shadow` lint.
**Catch:** Lint + review.

#### 41. Misusing ORMs (fetch-all-then-filter)
**What:** Loads table into memory to filter locally.
**Right:** Filter in DB (`where/limit/select`); check generated SQL.
**Catch:** Query-log assertion; review for `.find().filter(` without `where`.

#### 42. Failing to close resources
**What:** Open files/connections leak.
**Right:** `with` / `using` / `try-finally`; pool with limits.
**Catch:** `grep` for `open(` without `with`; connection-leak test.

#### 43. String concat in hot loops
**What:** `+=` in Java/C# loop thrashes GC.
**Right:** `StringBuilder` / `join()` / buffer.
**Catch:** Perf test on large N.

#### 44. Arrow anti-pattern (deep nesting)
**What:** 5-level `if/else`.
**Right:** Guard clauses / early returns (§15).
**Catch:** Complexity lint (cyclomatic); review.

#### 45. Misusing promises (`async` + `.then` mix)
**What:** Unhandled rejections, lost errors.
**Right:** One style per function; `await` + `try/catch`; return promises.
**Catch:** `no-floating-promises`; unhandled-rejection test.

#### 46. Mutating dict during iteration
**What:** `RuntimeError: dictionary changed size`.
**Right:** Iterate `list(d.keys())` copy or build new dict.
**Catch:** Mutation test.

#### 47. Misunderstanding truthiness (`[]`/`{}` truthy in JS)
**What:** `if ([])` is true.
**Right:** Explicit `arr.length === 0`, `Object.keys(o).length`.
**Catch:** Empty-input tests.

#### 48. Hardcoding file paths (`C:\...`)
**What:** Breaks on Linux/Docker.
**Right:** `path.join`, `pathlib`, env-based dirs (§4).
**Catch:** CI on Linux; `grep "C:\\\\"` fails.

#### 49. Inconsistent naming (`camel + snake` mix)
**What:** `userName` vs `user_name` same scope.
**Why:** Mixed sources.
**Right:** Linter (camel JS, snake Python) + formatter (§15).
**Catch:** Lint.

#### 50. Precision loss (large ints in JSON/JS)
**What:** `> 2^53-1` loses precision.
**Right:** `BigInt` / string IDs for snowflakes; `json` with stringified big ints.
**Catch:** Big-ID test.

---

### C. Security and Vulnerability Flaws (51–75)

> Treat every AI security suggestion as hostile until proven safe. Map to §8, §20.

#### 51. SQL injection (string concat)
```ts
// wrong: `SELECT * FROM users WHERE email='${email}'`
// right: .from("users").select().eq("email", email)  // parameterized
```
**Catch:** `grep` for template SQL + SAST (CodeQL/Semgrep).

#### 52. XSS (unescaped user HTML)
**Wrong:** `dangerouslySetInnerHTML={{__html: userInput}}`.
**Right:** Escape by default; sanitize (DOMPurify) + CSP.
**Catch:** XSS payload test (`<script>alert(1)</script>` renders as text).

#### 53. Insecure randomness (`Math.random` for secrets)
**Right:** `crypto.randomUUID()` / `secrets.token_urlsafe(32)`.
**Catch:** `grep "Math.random"` in auth/token paths fails CI.

#### 54. Hardcoding secrets in code
**Right:** Env + secret manager (§4); rotate if ever committed.
**Catch:** Gitleaks + history scan.

#### 55. Disabled SSL (`verify=False`)
**Right:** Never disable in prod; fix certs; pin CA bundle.
**Catch:** `grep "verify=False\|rejectUnauthorized.*false"` fails CI.

#### 56. Weak password hashing (MD5/SHA-1)
**Right:** `bcrypt` / `argon2` / provider Auth (don't store passwords yourself if avoidable).
**Catch:** `grep -ri "md5\|sha1"` in auth paths.

#### 57. CSRF (state-changing without token)
**Right:** SameSite cookies + CSRF tokens / double-submit for cookie auth; prefer SameSite=Lax + token.
**Catch:** Cross-origin POST test.

#### 58. IDOR (`/users/5` without ownership)
**Right:** `owner_id` check + RLS (§8); return 404 (not 403-leak) per policy.
**Catch:** Cross-user test (A fetches B → 404).

#### 59. Exposing stack traces in prod
**Right:** Generic `{error:{code,message}}`; full trace to Sentry only.
**Catch:** Prod-mode test asserts no `stack` in response.

#### 60. Permissive CORS (`*` + credentials)
**Right:** Allowlist origins; `*` never with credentials.
**Catch:** Preflight test from evil origin.

#### 61. Command injection (`os.system(userInput)`)
**Right:** Avoid shell; arg arrays + allowlist + sandbox.
**Catch:** `grep "os.system\|child_process.exec("` review gate.

#### 62. No rate limiting on auth
**Right:** IP throttling + lockout + CAPTCHA after N fails (§20).
**Catch:** 20 rapid logins → 429 test.

#### 63. XXE (external entities)
**Right:** Disable DTD/entities; use JSON where possible; updated parsers.
**Catch:** `<!ENTITY xxe SYSTEM "file:///etc/passwd">` test.

#### 64. Sensitive data in URLs (GET query)
**Right:** Body/headers only; logs redact query.
**Catch:** `grep` for password/token in GET; log inspection.

#### 65. Path traversal (`../`)
**Right:** Strip `..`, join within base dir, allowlist exts, random keys (§9).
**Catch:** `../../etc/passwd` download test.

#### 66. Open redirects (`?next=https://evil`)
**Right:** `safeNext()` (§8): allow `/` only, block `//`.
**Catch:** `?next=https://evil.com` stays in-app test.

#### 67. Default credentials (`admin/password`)
**Right:** Random on first boot + force rotate; no defaults in compose.
**Catch:** `grep -ri "password.*admin\|admin.*password"` in configs.

#### 68. Improper JWT validation (`none` alg)
**Right:** Verify alg + signature + exp + iss/aud with maintained lib; never accept `none`.
**Catch:** `none`-alg token rejected test.

#### 69. Missing security headers (CSP/HSTS)
**Right:** `Content-Security-Policy`, `Strict-Transport-Security`, `X-Frame-Options`, `nosniff`.
**Catch:** Header assertion test (`securityheaders.com` scan).

#### 70. Mass assignment (`req.body → ORM` unfiltered)
**Right:** Allowlist fields (Zod `pick`); never bind `is_admin`.
**Catch:** `is_admin:true` in body ignored test.

#### 71. Timing attacks (`==` on hashes)
**Right:** `crypto.timingSafeEqual` / `hmac.compare_digest`.
**Catch:** Review auth compare paths.

#### 72. Insecure deserialization (`pickle` / unsafe YAML)
**Right:** JSON + schema; `yaml.safe_load` only; sign if needed.
**Catch:** `grep "pickle.loads\|yaml.load("` without safe.

#### 73. SSRF (fetch internal metadata `169.254.169.254`)
**Right:** Allowlist egress, block private ranges, no redirects to internal.
**Catch:** SSRF payload test to metadata IP blocked.

#### 74. Sessions not invalidated on logout
**Right:** Server blacklist / rotate + short TTL + refresh rotation.
**Catch:** Use token after logout → 401 test.

#### 75. Excessive permissions (`*/*` IAM/RBAC)
**Right:** Least privilege per service; review quarterly.
**Catch:** IAM diff review; `*` requires justification.

---

### D. Environment, Dependency, and Testing Failures (76–100)

#### 76. Hallucinating npm/pip packages
**What:** `npm i super-auth-magic` doesn't exist → typosquat risk.
**Right:** Verify on npmjs/pypi; check downloads + repo + maintenance.
**Catch:** Install fails CI if unknown; require registry link in PR.

#### 77. Missing version pinning
**What:** `*` ranges → future breakage.
**Right:** Lock files committed; `npm ci` / hashed requirements (§13).
**Catch:** CI frozen install; Dependabot for updates, not floating.

#### 78. Ignoring OS differences (bash-only scripts)
**Right:** POSIX + Windows-tested scripts or Node/Python cross-platform tasks.
**Catch:** CI matrix (ubuntu + windows) or container-only scripts.

#### 79. Bloated Docker images (root + build tools in prod)
**Right:** Multi-stage (§17), non-root `USER`, `.dockerignore`, slim base.
**Catch:** Image-size budget + `whoami` != root test.

#### 80. Incomplete mocking (live network/DB in unit tests)
**Right:** `jest.mock` / `unittest.mock`; test DB in integration only.
**Catch:** Network-disabled unit run (assert no egress).

#### 81. Tautological tests (`assert True`)
**Right:** Assert behavior + boundaries + failures (§14).
**Catch:** Mutation testing / coverage + review ("what would make this fail?").

#### 82. Flaky E2E (`sleep(5)`)
**Right:** Auto-wait / `expect.poll` / `toBeVisible`, retries only on CI.
**Catch:** 10x repeat run; forbid fixed sleeps via lint.

#### 83. Interactive steps in CI (`apt-get` without `-y`, prompts)
**Right:** Non-interactive flags, cached setup actions.
**Catch:** CI dry-run; headless test.

#### 84. Destructive git advice (`push -f` for small conflicts)
**Right:** `pull --rebase`, resolve, force-with-lease only on personal branch.
**Catch:** Branch protection (no force-push to `main`).

#### 85. Missing `.gitignore` (commits `node_modules/.env`)
**Right:** Template from §4.2; `git status` clean check in README.
**Catch:** CI asserts no tracked `.env` / `node_modules`.

#### 86. Wrong log levels (PII at INFO)
**Right:** DEBUG (dev only) / INFO (events) / WARN / ERROR; redact PII (§19).
**Catch:** Log review; `grep` for password/email in logs.

#### 87. Silent background failures (cron with no alerts)
**Right:** Job table + Sentry + dead-man alert (§10, §19).
**Catch:** Fail-job staging test asserts alert fires.

#### 88. Hardcoded IPs in config
**Right:** DNS + env (§4); service discovery.
**Catch:** `grep -E "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+"` review.

#### 89. Dev build shipped to prod (unminified)
**Right:** `npm run build` + `NODE_ENV=production`; source maps private.
**Catch:** Bundle-size + `__NEXT_DATA__` / sourcemap check.

#### 90. Manual SQL instead of migrations
**Right:** Migrations only (§7); `db push` via CLI; review SQL.
**Catch:** CI diff: no direct prod DDL; migration lint.

#### 91. No graceful shutdown (drop connections on SIGTERM)
**Right:** Drain + `server.close()` + job checkpoint; K8s `terminationGracePeriod`.
**Catch:** SIGTERM test (inflight request completes).

#### 92. Node version mismatch (code needs 18+, image has 14)
**Right:** `.nvmrc` + `engines` + Docker tag match; matrix test.
**Catch:** `node -v` assertion in Dockerfile + CI.

#### 93. Testing internals over behavior (private state)
**Right:** Test public API/contracts; refactor-safe (§14).
**Catch:** Review: "does this test break on valid refactor?".

#### 94. No formatter/linter configured
**Right:** Prettier + ESLint + Black/Ruff from day one (§15).
**Catch:** CI `lint` + `format --check` gates.

#### 95. Broken `tsconfig` paths (editor green, build red)
**Right:** `paths` + `baseUrl` + CI `tsc --noEmit` (never editor-only).
**Catch:** Clean-clone `typecheck` in CI (§16).

#### 96. Hardcoded `localhost:3000` in client
**Right:** `NEXT_PUBLIC_APP_URL` / relative `/api/*` (§4).
**Catch:** Staging test on non-localhost domain.

#### 97. Non-deterministic seeds (random QA data)
**Right:** Seeded faker + fixed fixtures; random only with seed logged.
**Catch:** Two seed runs produce same DB.

#### 98. Env parity gap (SQLite local, Postgres prod)
**Right:** Same engine via Docker/CLI (§17); or document incompatibilities + integration test on Postgres.
**Catch:** CI runs Postgres service, not SQLite.

#### 99. Zero docs (1000s lines, no README/JSDoc)
**Right:** README + API + ML card + inline `why` (§22).
**Catch:** Docs checklist in PR template.

#### 100. Blind code merging (`// ... rest of code` deletes logic)
**What:** Apply AI diff that drops surrounding business rules.
**Right:** Diff review line-by-line; run full tests; never paste placeholders.
**Catch:** `git diff --stat` + test + staging click-through before merge.

---

### 25.3 System prompt to reduce AI errors (copy-paste for your assistant)

```
You are a senior full-stack + ML engineer. Constraints:
- Verify versions against package.json/requirements before suggesting APIs.
- Never hardcode secrets, IPs, or localhost; use env (env.example).
- All DB access must include owner scoping + RLS-safe patterns.
- Paginate lists, set timeouts/retries, handle INSUFFICIENT/no-provider.
- Prefer parameterized queries, escape HTML, validate with Zod/Pydantic.
- Provide typecheck-clean TypeScript (strict) or typed Python.
- Include unit test + error path for every function.
- If unsure about a cloud service, package, or method, say UNKNOWN and ask.
- Output: files changed, full code, commands to verify (typecheck/test).
```

### 25.4 Verification workflow for any AI-generated PR

1. `npm run typecheck` + `npm run lint` + `pytest` (as applicable) — must be green.
2. Run Appendix D checklist (below) — reviewer ticks each box.
3. Staging deploy + click-through core loop + auth-boundary test.
4. Record metrics (latency, eval score) if ML/LLM touched.

---

## 26. Additional AI Mistakes by Root Cause (50 Catalog)

> Companion to §25 (100 mistakes). These 50 focus on *why* the failure happens: flawed reasoning, output formatting quirks, missing project context, insecure/compliance-blind defaults, and performance/maintenance blind spots. Numbered 101–150 for tracking. Each entry: **What → Why → Wrong vs Right → How to catch**.

### 26.1 Logic and Semantic Errors (101–110)

Root cause: pattern-matching over reasoning. Code runs but means the wrong thing, especially on boundaries.

#### 101. Failing on edge cases (empty / null / zero / negative)
**What:** Happy-path only; crashes or misbehaves on `[]`, `null`, `0`, `-1`.
**Why:** Typical scenarios dominate training examples.
```ts
// wrong: function avg(a:number[]){ return a.reduce((x,y)=>x+y)/a.length } // NaN on []
// right: if (!a.length) return 0; // + explicit tests for [], [0], [-5]
```
**Catch:** Boundary unit tests mandatory: `[], null, 0, -1, max+1` for every util/API (§14).

#### 102. Silent logic failures (runs clean, wrong result)
**What:** No exception, but wrong output on edge slices; hardest to trace.
**Why:** Syntactically valid ≠ semantically correct; no runtime check.
**Right:** Property tests + golden pairs + assertions on invariants (`assert out.length <= in.length`).
**Catch:** Mutation testing; staging diff vs known-good output; log input/output hashes for ML.

#### 103. Pattern-matching over reasoning (flawed loops / conditionals)
**What:** `for` + `if` shaped like example but inverted for your problem.
**Why:** Copies surface shape, not intent.
**Right:** Restate intent in comment first, then code; truth-table for conditions.
**Catch:** Reviewer asks "walk through with 3 concrete inputs"; table-test all branches.

#### 104. Early-return mismanagement (especially in loops)
**What:** `return` inside loop exits whole function instead of `continue`; or missing return leaks `undefined`.
```ts
// wrong: for (const u of users) { if (!u.active) return null } // aborts on first inactive
// right: const active = users.filter(u => u.active); if (!active.length) return null
```
**Catch:** Loop tests with mixed valid/invalid items; lint `consistent-return`.

#### 105. Constant-value errors (wrong literals / args)
**What:** Wrong timeout, status code, threshold, enum (`status: "done"` vs `"completed"`).
**Why:** Guesses literals from similar APIs.
**Right:** Import constants from schema/types; never invent strings (Zod enum).
**Catch:** Typecheck + contract test against DB/API enum values.

#### 106. Guard clauses that hide root cause
**What:** `if (!x) return []` silences upstream bug (null from DB, failed fetch).
**Why:** Optimizes for "no crash" over "correct".
**Right:** Fix upstream (why null?); guard only with logged warning + metric.
```ts
if (!rows) { logger.warn({ reqId }, "items query returned null, expected []"); return [] }
```
**Catch:** Warn-level log review; null should be impossible → add NOT NULL / default.

#### 107. Wrong logical direction (long-prompt drift)
**What:** Implements inverse logic after 200+ word prompt (allow vs deny, ascending vs descending).
**Why:** Attention dilutes over long context.
**Right:** Split prompts: goal (≤50 words) + constraints + 2 examples; restate expected I/O.
**Catch:** Acceptance test from prompt's own example before merge.

#### 108. Memory / recursion non-termination
**What:** `while` / recursion with no reachable base case.
**Why:** Omits termination proof.
**Right:** Explicit variant (counter/depth cap) + iterative fallback.
```python
# right: def walk(n, depth=0):
#   assert depth < 1000, "cycle?"
```
**Catch:** Timeout + depth-cap test; fuzz with cyclic input.

#### 109. Condition errors (missing / inverted `if`)
**What:** Omits `owner check`, flips `>` to `>=`, drops `&& scope`.
**Why:** Drops "secondary" conditions for brevity.
**Right:** Decision table per handler (auth? ownership? scope? state?); test each combo.
**Catch:** Branch-coverage + cross-user/edge matrix (§14).

#### 110. Return-shape errors (wrong value / format)
**What:** Returns string instead of `{data}`, wrong date format, unwrapped JSON.
**Why:** Mimics nearest example, not your contract.
**Right:** Shared response type + Zod `parse` before `return`; contract test.
**Catch:** Typecheck + snapshot of API envelope (`{data}` vs `{error}`).

---

### 26.2 Syntax and Output Quirks (111–120)

Root cause: generation formatting + multi-file coordination failures. Breaks builds, parsers, and merges.

#### 111. JSON parsing failures (markdown fences + chatter)
**What:** Returns ```` ```json {...} ``` + "Here is..." `` instead of raw JSON → `JSON.parse` crashes.
**Why:** Chat style leaks into machine output.
**Right:** Backend strips fences + extracts first `{...}`/`[...]` (`parseJsonResult`), validates with Zod; prompt demands "JSON only, no markdown".
```ts
const clean = text.replace(/```json|```/g, "").trim();
return JSON.parse(clean.slice(clean.indexOf("{"), clean.lastIndexOf("}") + 1));
```
**Catch:** Fuzz LLM output (fences, prefix text); contract test rejects non-JSON.

#### 112. The "laziness" anti-pattern (empty stubs)
**What:** `function computeTax(){ /* Your logic goes here */ }`.
**Why:** Avoids hard work when uncertain.
**Right:** Reject stubs; require TODO with owner + test that fails (`expect(fn).toThrow(/not implemented/)` → forces fill).
**Catch:** `grep -r "Your logic goes here\|TODO.*AI"` fails CI if unclaimed.

#### 113. Hallucination coding (nonexistent vars / funcs)
**What:** Calls `db.getUserOrThrow()` that doesn't exist.
**Why:** Plausible-name invention (§25.1).
**Right:** Typecheck + go-to-definition check; import only from existing files.
**Catch:** `tsc --noEmit` + `eslint no-undef` blocks merge.

#### 114. Comment-only output on long prompts
**What:** Entire block is `// ...` with no executable code.
**Why:** Overload → safe fallback to comments.
**Right:** Chunk prompt (one file/function per request); demand diff, not essay.
**Catch:** Line-count gate (executable lines > 0); reject comment-only PRs.

#### 115. Method-call errors (wrong name / args / target)
**What:** `stripe.charges.create({amount})` on new SDK expecting PaymentIntents.
**Why:** Version drift + similar names.
**Right:** Check installed version's docs/signature; wrap external calls in `lib/` adapter with tests.
**Catch:** Adapter unit test with mocked SDK; docs link required.

#### 116. Missing steps (skipped migration / import / wiring)
**What:** Gives handler but omits migration, env, route registration.
**Why:** Answers visible question, forgets prerequisites.
**Right:** Definition-of-done per task: migration + handler + wiring + test + docs (§23).
**Catch:** Checklist review ("where is migration/env/test?"); clean-clone boot test.

#### 117. Multi-hunk errors (spanning blocks)
**What:** 3-file change where 2 hunks are subtly wrong; large repair cost.
**Why:** No global consistency check across hunks.
**Right:** Small diffs (one concern per PR); full test + typecheck after each hunk.
**Catch:** Diff-size budget (>300 lines → split); per-hunk test.

#### 118. Unnecessary fallback logic (clutter)
**What:** 5 nested `try/catch` + `|| "default"` hiding real errors.
**Why:** Defensive verbosity.
**Right:** One fallback at boundary with logging; fail loud inside.
**Catch:** Review: "which fallback ever triggers? delete the rest".

#### 119. Loop-boundary mistakes (`<` vs `<=`, off-by-N)
**What:** Pagination skips/duplicates rows; chunker drops last chunk.
**Why:** Manual index math.
**Right:** Prefer cursors/`slice`/library chunk; boundary tests (0,1,page-size,page-size+1).
**Catch:** Pagination round-trip test (fetch all pages, assert exact set).

#### 120. Import errors (wrong path / missing / circular)
**What:** `@/lib/foo` doesn't exist, case mismatch, or cycle.
**Why:** No filesystem view.
**Right:** Alias `@/*` verified (§3), case-sensitive CI (Linux), `madge --circular`.
**Catch:** Clean `tsc` + Linux CI build (catches Windows-only paths).

---

### 26.3 Context and Architecture Blindness (121–130)

Root cause: no holistic project model — goals, conventions, existing utils, non-functional needs, or context limits.

#### 121. Context blindness (no holistic vision)
**What:** Solves ticket literally, breaks roadmap (e.g. adds SQLite when standard is Postgres).
**Why:** Sees snippet, not system.
**Right:** Link ADR + ERD in prompt; require "impact on existing modules" section in PR.
**Catch:** Architecture review for any new store/queue/auth choice.

#### 122. Violating established conventions
**What:** `snake_case` in TS repo, REST in GraphQL codebase, custom modal vs design system.
**Why:** Generic defaults override local style.
**Right:** Prompt with 2 local examples; lint + component allowlist (`components/ui` only).
**Catch:** Lint + visual review; `grep` for non-token colors/hardcoded styles.

#### 123. Duplicating code (reinvents existing util)
**What:** New `formatDate()` when `lib/date.ts` exists.
**Why:** Unaware of codebase.
**Right:** Search before generate (`grep formatDate`); utils index in README.
**Catch:** Duplication detector (jscpd) + reviewer question "does util exist?".

#### 124. Ignoring non-functional requirements (scale / compliance / a11y)
**What:** Works for 10 rows, fails at 10M; no audit/PII handling.
**Why:** Functional-only prompt.
**Right:** Prompt must include NFRs: p95, rows, retention, compliance, a11y.
**Catch:** NFR checklist in PR template (§24 + App. D).

#### 125. Context-window drop-off (forgets early constraints)
**What:** Follows last paragraph, violates first (e.g. "Postgres only" → suggests Mongo).
**Why:** Recency bias near limit.
**Right:** Put hard constraints first + last; split into ≤2k-token tasks with summary carryover.
**Catch:** Constraint-retest (grep for forbidden tech after generation).

#### 126. Readability discrepancies (looks clean, reads alien)
**What:** Consistent style but wrong structure (deeply nested, cryptic names).
**Why:** Mimics surface formatting, not local clarity patterns.
**Right:** Max function 50 lines, early returns, domain names; formatter + human rename pass.
**Catch:** Read-aloud review; complexity budget.

#### 127. Overcomplicating simple workflows
**What:** State machine + 3 abstractions for a boolean toggle.
**Why:** Fills uncertainty with machinery.
**Right:** YAGNI: simplest that passes tests; delete speculative layers.
**Catch:** "Delete half — do tests still pass?" review.

#### 128. Garbage code (dead snippets, more common in small models)
**What:** Unused vars, unreachable branches, meaningless helpers.
**Why:** Low-capacity sampling noise.
**Right:** Prefer larger reviewed models for code; always `tsc --noUnusedLocals` + coverage.
**Catch:** `noUnusedLocals` + dead-code scan; coverageAnnotates unused.

#### 129. Prompt-length degradation (>50 words → garbage)
**What:** Long mega-prompt yields incoherent output.
**Why:** Instruction overload.
**Right:** 50-word task + bullets + I/O example; chain prompts (spec → code → test).
**Catch:** If output garbage, halve prompt and retry (documented workflow §25.4).

#### 130. Unnecessary abstractions / special cases
**What:** Factory-of-factories, one-off `if (user==="bob")`.
**Why:** Premature generalization + overfitting to example.
**Right:** Rule of three (abstract on 3rd use); config over special-case.
**Catch:** Abstraction review; special-case `grep` for hardcoded names.

---

### 26.4 Security, Bias, and Compliance (131–140)

Root cause: training-data biases + no regulatory model. Assume hostile until audited. Extends §25 C (51–75).

#### 131. Reproducing insecure training data (unintentional)
**What:** Copies 2015-era crypto/auth patterns that were common but unsafe.
**Why:** Frequency ≠ safety.
**Right:** SAST + dependency age check + "is this 2024+ recommended?" prompt clause.
**Catch:** Semgrep/CodeQL ruleset for crypto/auth/storage.

#### 132. Leaking internals via error handlers
**What:** `res.json({error: e.stack, query, env})`.
**Why:** Helpful-debug default.
**Right:** Generic client message + full detail to Sentry with request ID (§19–§20).
**Catch:** Prod-mode error test asserts no `stack|query|env` in body.

#### 133. Type-juggling vulnerabilities (`==` in JS/PHP)
**What:** `"0" == false` true → auth bypass.
**Why:** Loose-equality snippets.
**Right:** `===` everywhere + Zod coercion explicit; `eqeqeq` lint.
**Catch:** `eslint eqeqeq` + auth edge tests (`"0", "", null, []`).

#### 134. Hardcoding credentials / insecure API handling
**What:** `apiKey = "sk-live-..."` + committed.
**Why:** Example convenience (§25 #54 repeat — enforced here as root-cause class).
**Right:** Env + rotation + gitleaks pre-commit (§4, §20).
**Catch:** Secret scan blocks push; history rewrite + rotate if leaked.

#### 135. Encoding harmful biases (ranking / filtering / language)
**What:** Resume ranker penalizes names/gaps; toxic completions.
**Why:** Biased corpora.
**Right:** Bias eval slices, human review, allow appeal/override, log decisions.
**Catch:** Slice metrics + red-team prompts; feedback channel (§10).

#### 136. Copyright / license violations (copied proprietary code)
**What:** Verbatim GPL/copyleft or commercial snippet in MIT repo.
**Why:** Memorization without provenance.
**Right:** SCA scan (FOSSA/Snyk), rewrite vs import with compatible license, attribute.
**Catch:** License scan in CI; large verbatim-block detector.

#### 137. Assuming AI safety (≈half suggestions vulnerable)
**What:** "It compiled, so it's safe."
**Why:** Fluency mistaken for correctness.
**Right:** Default-deny: SAST + manual sec review for auth/crypto/uploads/AI (§20).
**Catch:** Sec checklist gate (App. D) — no skip.

#### 138. Improper authorization + injection (paired flaw)
**What:** Missing owner check *and* raw SQL in same handler.
**Why:** Two blind spots compound.
**Right:** Auth-first template (§6.2) + parameterized queries + IDOR test.
**Catch:** Combined test: user B + injection payload → 404, no data.

#### 139. Compliance blindness (GDPR / HIPAA / SOC2 / AI Act)
**What:** Logs PII, no deletion/export, no consent, trains on prod PII.
**Why:** No regulatory model.
**Right:** Data map + retention + DSR endpoints (`DELETE /api/account`, export), PII minimization, DPA for providers.
**Catch:** Compliance review per feature; PII `grep` in logs/DB.

#### 140. Model collapse via bad-code feedback loop
**What:** AI bugs committed → future models train on them → ecosystem degrades.
**Why:** Polluted commons.
**Right:** Human-reviewed commits only; don't bulk-commit unreviewed AI output to public repos.
**Catch:** Review + eval gates; prefer upstream libs over pasted AI snippets.

---

### 26.5 Performance, Tooling, and Maintenance (141–150)

Root cause: no cost/latency/version empathy; fixes one prompt while breaking others; erodes skills.

#### 141. Unoptimized queries (missing indexes / full scans)
**What:** Works on 100 rows, times out at 1M under stress.
**Why:** No `EXPLAIN` step.
**Right:** Index `(owner_id, created_at)`, `select` minimal columns, paginate; load test (§7, §21).
**Catch:** `EXPLAIN ANALYZE` + k6/smoke with 10x data in staging.

#### 142. Inefficient data handling (`equals/hashCode` bloat, over-fetch)
**What:** Extra fields in equality, `SELECT *` for one column, JSON blob per row.
**Why:** Copies verbose examples.
**Right:** Minimal equality keys, projection queries, normalized hot paths.
**Catch:** Payload-size + query-time budgets in tests.

#### 143. Async logic failures (blocked loop / unhandled rejection)
**What:** `await` in tight loop blocks; missing `catch` crashes worker.
**Why:** Sequential mental model of async.
**Right:** `Promise.all` with concurrency cap (p-limit), per-item try/catch, queue for heavy fan-out.
**Catch:** Unhandled-rejection listener fails test; concurrency test.

#### 144. Outdated dependencies (recommends EOL libs)
**What:** `node-sass`, Python 2-era packages.
**Why:** Old popularity.
**Right:** Check maintenance (commit <6mo, downloads, advisories); Dependabot (§13).
**Catch:** `npm outdated` + `pip audit` in CI.

#### 145. Knowledge-cutoff blindness (unaware of deprecation)
**What:** Uses removed flag/method confidently.
**Why:** No live version feed.
**Right:** Prompt must include installed versions; verify against local `node_modules`/docs.
**Catch:** Typecheck + runtime smoke on exact lockfile versions.

#### 146. Deprecated APIs that "work until they don't"
**What:** Functions on removal timeline; fail on minor upgrade.
**Why:** Lagging examples.
**Right:** Prefer stable APIs; track deprecation warnings as CI failures (`--throw-deprecation`).
**Catch:** Deprecation-warning grep fails build.

#### 147. Old syntax (var, callbacks, legacy config)
**What:** `var`, callback hell, Webpack 4 config for Vite repo.
**Why:** Old snippets rank high.
**Right:** Modern baseline in prompt (ES2023, `let/const`, async/await, current bundler).
**Catch:** Lint rules (`no-var`, `prefer-const`) + config schema validation.

#### 148. Whac-a-mole prompt iteration (no eval set)
**What:** Fixes example A, breaks B–F silently.
**Why:** No regression harness.
**Right:** Golden/eval set first (§11.5, §14); run full suite per prompt change.
**Catch:** Eval diff required in PR ("before/after on 20 cases").

#### 149. Bypassing static analysis (syntactically clean, logically wrong)
**What:** Compilers/linters pass; behavior wrong.
**Why:** Tools check syntax/types, not intent.
**Right:** Behavior tests + property tests + human walkthrough (§14); don't rely on green lint alone.
**Catch:** Mutation score + staging behavioral check.

#### 150. Developer skill degradation (over-reliance)
**What:** Accepts output without understanding; debugging atrophies.
**Why:** Frictionless generation skips learning.
**Right:** Require engineer to explain diff in PR; rotate "no-AI" tasks; pair review.
**Catch:** PR template: "explain in 3 sentences why this is correct" (App. B.4 extended).

---

### 26.6 Master map: 150 mistakes at a glance

| # | Category | Use with |
|---|----------|----------|
| 1–25 | Architecture / system design | §2–§9, §21 |
| 26–50 | Code generation / logic | §5–§6, §14–§15 |
| 51–75 | Security flaws | §8, §20 |
| 76–100 | Env / dependency / testing | §12–§14, §16–§17 |
| 101–110 | Logic / semantic (root-cause) | §6, §14 + boundary tests |
| 111–120 | Syntax / output quirks | §6, §11.3, §16 |
| 121–130 | Context / architecture blindness | §2–§3, ADRs |
| 131–140 | Security / bias / compliance | §8, §20 + App. D |
| 141–150 | Performance / tooling / maintenance | §7, §13, §19, §21 |

**Checklist (§26):**

- [ ] Boundary tests for every AI util/handler (`[], null, 0, -1`)
- [ ] JSON-output contract tests (fences/chatter stripped, Zod-validated)
- [ ] No stubs/placeholders merged; diff size budgeted
- [ ] Local conventions + existing utils checked before generating
- [ ] Security/bias/compliance pass (App. D) + PII/bias eval where relevant
- [ ] Perf + version checks (EXPLAIN, budgets, lockfile versions, deprecation warnings as failures)
- [ ] Eval set run per prompt change (no whac-a-mole); engineer explains diff in own words

---

## Appendix D: AI-Code Review Checklist

Copy into every AI-assisted PR:

- [ ] Versions verified (`package.json` / docs link, no deprecated API)
- [ ] No hallucinated services/packages/methods (registry/docs checked)
- [ ] No secrets/IPs/localhost hardcoded; env used
- [ ] Auth + ownership + RLS considered; IDOR tested (user A vs B)
- [ ] Inputs validated server-side; SQL parameterized; HTML escaped
- [ ] Pagination, timeouts, retries, backoff present where needed
- [ ] Async used for slow jobs; idempotency key for payments/webhooks
- [ ] Timezones UTC; indexes added for new queries (`EXPLAIN` checked)
- [ ] Errors handled (no `pass`/`catch{}`), logged with request ID, user-safe message
- [ ] Tests added: unit (boundaries) + integration (401/404/422) + e2e if UI; ML eval/golden set if touched
- [ ] Docker/CI green: typecheck, lint, build, tests, image non-root, graceful shutdown
- [ ] Docs updated: README/API/ML card/CHANGELOG if behavior changed
- [ ] Staging verified; rollback plan noted
- [ ] Boundary + JSON-contract + eval-set checks from §26 passed
- [ ] Engineer explains diff in own words (no blind merge)

---

## Appendix E: AI Output Guardrails

Concrete repo gates that catch §25–§26 mistakes automatically:

```bash
# Block secrets, weak patterns, and banned APIs
npx gitleaks detect --source . --verbose
grep -r "verify=False\|Math.random().*token\|password.*admin" --include="*.ts" --include="*.py" . && exit 1
grep -r "Your logic goes here\|TODO.*AI" --include="*.ts" --include="*.py" . && exit 1
```

```json
// tsconfig.json — catch dead code + loose types
{ "compilerOptions": { "strict": true, "noUnusedLocals": true, "noFallthroughCasesInSwitch": true } }
```

```yaml
# CI gates (add to .github/workflows/ci.yml)
- run: npm run typecheck
- run: npx eslint . --max-warnings=0   # includes eqeqeq, no-var, no-fallthrough, no-shadow
- run: npm run test -- --run --coverage
- run: python -m pytest ml-service/tests -q
- run: python ml-service/evaluate.py --check-threshold
```

- Prompts ≤50 words + I/O example; constraints first + last (§26.3).
- One concern per AI PR; diff >300 lines → split (§26.2).
- Golden/eval set required for prompt/ML changes; report before/after (§26.5).
- Monthly rotation: one "no-AI" task per dev to preserve debugging skill (§26.5 #150).

---

*End of guide. Start small, ship the loop, instrument everything, then scale what you measure. Treat AI as a fast drafter and yourself as the reviewer who guarantees correctness, security, and operability.*
