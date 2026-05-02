# darkpoc-template-fastapi-maf-copilotkit

GitHub template repo for the **dark POC factory**. Every brief the factory accepts is scaffolded from this template via `gh repo create --template`. The factory's agent then writes feature code on top.

> **If you're an AI agent reading this**: read `CLAUDE.md` next. It documents the stack conventions, the bug mitigations you must preserve, and how to add features via TDD.

## Stack

- **Backend**: FastAPI · Microsoft Agent Framework (Python) · Anthropic Claude · AG-UI protocol
- **Frontend**: Next.js 15 · React 19 · CopilotKit · `@ag-ui/client`
- **Tests**: pytest · vitest
- **CI**: GitHub Actions (test + multi-arch docker build) · branch protection on `main`

## Layout

```
backend/    FastAPI + MAF agent at /agent (AG-UI)
frontend/   Next.js + CopilotKit chat, /api/copilotkit proxies to backend
infra/      github-branch-protection.json (applied at repo creation)
.github/    ci.yml + claude.yml workflows
CLAUDE.md   conventions for AI coding agents
```

## Run locally

```bash
cp backend/.env.example backend/.env
# set ANTHROPIC_API_KEY in backend/.env

cp frontend/.env.example frontend/.env.local

docker compose up --build
# backend  → http://localhost:8000
# frontend → http://localhost:3000
```

Open `http://localhost:3000` and chat with the example agent.

## Run tests

Backend (pytest):
```bash
cd backend && docker run --rm -e ANTHROPIC_API_KEY=sk-ant-test-not-used $(docker compose -f ../docker-compose.yml config | grep -A1 '^\s*backend:' | tail -1 | awk '{print $2}' || echo "darkpoc-backend") pytest -v
```
Or just `docker compose run --rm backend pytest -v` if you have a real `.env` set up.

Frontend (vitest):
```bash
cd frontend && npm install && npm run test -- --run
```

## Adding features

See `CLAUDE.md`. Short version: write a failing test first, implement to green, refactor. The factory's `refiner` subagent then runs 5 critique-and-improve passes after green tests, recorded in `iterations.md`.

## License

POC factory output — license whatever you ship to whatever you choose. The template itself has no opinion.
