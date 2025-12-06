# Tapestry

Simulate your life and generate branching future possibilities. Tapestry pairs an AI interview with a 3D life-graph so you can explore next steps before you take them.

<img src="public/tapestry.png" alt="Tapestry" width="80%">

---

## Overview

- Interactive life simulator: speak or type with an agent, then see possible futures visualized as a branching graph.
- Full-stack: Next.js 15/React 19 frontend + FastAPI backend with streaming agents.
- Data-backed: PostgreSQL via Drizzle on the frontend and psycopg2 on the backend keeps nodes and links consistent.

---

## Principles

1. **Future-first** – Every action should reveal multiple plausible futures, not a single line.
2. **Context-rich** – Personal background captured in the interview steers all generations.
3. **Human-in-the-loop** – You can prune, extend, or rerun paths instead of accepting a fixed plan.

---

## Architecture

### Frontend (Next.js)

- Next.js 15 + React 19 with Tailwind CSS 4.
- NextAuth Google OAuth for sign-in; guards redirect to interview or life view.
- 3D visualization with `react-force-graph-3d`/Three.js for nodes, links, and “Now” anchoring.
- Optional speech-to-text for the interview; SSE client for live agent responses.

### Backend (FastAPI + ADK agents)

- FastAPI service (`backend/main.py`) streaming via SSE and receiving messages via HTTP.
- Agent stack (ADK) orchestrates interviewer, node maker, and reviewer to grow paths.
- Relational storage with PostgreSQL (`DATABASE_URL`) using psycopg2; shared with frontend.
- CORS open to `http://localhost:3000` for local dev.

### Data & Graph

- Nodes/links persisted in Postgres; “Now” is fixed, future nodes branch with time offsets.
- Drizzle schema and migration helpers on the frontend (`pnpm db:generate|push|migrate`).
- Life graph UI lets you click to expand futures, delete, or inspect nodes.

### Flow

1. Sign in with Google.
2. Complete the AI-led interview (text or voice) to capture background/goals.
3. Backend agents generate branching nodes; frontend renders and lets you extend or prune paths.

---

## Setup

**Requirements**

- Node.js 20+
- pnpm 10.14+
- Python 3.10+ (backend) and virtualenv
- PostgreSQL (local or Docker)
- Google API key for the agent (Gemini) and Google OAuth credentials for NextAuth

**1) Frontend env** (`.env` in project root)

```
AUTH_SECRET=your_session_secret
GOOGLE_CLIENT_ID=your_google_oauth_client_id
GOOGLE_CLIENT_SECRET=your_google_oauth_client_secret
DATABASE_URL=postgresql://USER:PASSWORD@localhost:5432/tapestry
NEXTAUTH_URL=http://localhost:3000
BACKEND_URL=http://localhost:8000
```

**2) Backend env** (`.env` in project root, used by `backend/main.py`)

```
GOOGLE_API_KEY=your_gemini_api_key
DATABASE_URL=postgresql://USER:PASSWORD@localhost:5432/tapestry
```

**3) Install deps**

```bash
pnpm install
python3 -m venv backend/venv
source backend/venv/bin/activate
pip install -r backend/requirements.txt
```

**4) Start Postgres**

```bash
./start-database.sh           # uses DATABASE_URL
# or: docker-compose up -d    # uses docker-compose.yml
```

**5) Run backend**

```bash
source backend/venv/bin/activate
uvicorn backend.main:app --host 0.0.0.0 --port 8000 --reload
```

**6) Run frontend**

```bash
pnpm dev
```

Visit `http://localhost:3000`.

**One-shot (docker/runtime helper)**

```bash
./start-services.sh
```

---

## Useful scripts

- `pnpm db:generate | db:push | db:migrate` – manage Drizzle migrations.
- `pnpm lint`, `pnpm check`, `pnpm format:write` – quality passes.
- `curl -H "Content-Type: application/json" http://localhost:8000/adk/events/123` – quick SSE sanity check.

---

## What you get

- AI-assisted interview that adapts to your background.
- Branching life graph with editable paths to explore multiple futures.
- Local-first, container-friendly stack for fast experimentation and deployment.

