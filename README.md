# ? PulseFit AI

[![Python 3.12+](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/)
[![LLM: Google Gemini](https://img.shields.io/badge/LLM-Google%20Gemini-orange.svg)](https://aistudio.google.com/)
[![Database: PostgreSQL](https://img.shields.io/badge/database-PostgreSQL-blue.svg)](https://neon.tech)
[![Queue: Upstash QStash](https://img.shields.io/badge/queue-Upstash%20QStash-red.svg)](https://upstash.com)
[![Deployment: Vercel](https://img.shields.io/badge/deployment-Vercel-black.svg)](https://vercel.com)
[![Tests: Pytest](https://img.shields.io/badge/tests-199%20passed-brightgreen.svg)](https://docs.pytest.org/)

**PulseFit AI** is an autonomous, personal AI fitness intelligence agent and Telegram companion. It monitors completed workouts (weight training & endurance running) via Strava webhooks, computes deep mathematical analytics within ~60 seconds, delivers unprompted analytical briefings on Telegram, and holds a 30-minute interactive conversation to answer questions, compare training history, or chart performance trends.

---

## ?? Key Architectural Highlights

- **Zero-LLM Math Isolation**: All arithmetic (tonnage, e1RM, fatigue drop-offs, LTHR zones, aerobic decoupling, ACWR workload ratios) is computed in pure, unit-tested Python. Google Gemini strictly interprets pre-calculated metrics into actionable coaching prose.
- **Single Source of Truth (Strava + Hevy Parser)**: Weight training sets are parsed directly from Strava's activity descriptions (as written by Hevy exports), eliminating paid API dependencies.
- **Dynamic 3-Tier Muscle Mapping**: Resolves exercise names using a local static table, learned PostgreSQL history, and zero-shot Gemini classification for unmapped movements.
- **Asynchronous Queue Architecture**: Sub-second webhook acknowledgements (<200ms) with event processing handled via Upstash QStash and Vercel background workers.

---

## ??? Architecture & Pipeline Workflow

```mermaid
flowchart TD
    subgraph Ingestion["1. Ingestion Layer"]
        A[Strava Webhook] -->|Event Notification| C[api/index.py Webhook]
        B[Telegram Webhook] -->|User Message| C
    end

    subgraph Queue["2. Queueing & Security"]
        C -->|< 200ms ACK| D[Upstash QStash Queue]
        D -->|JWT Authenticated POST| E[Vercel Worker Endpoint /api/worker]
    end

    subgraph Analytics["3. Analytics Engine (Pure Python)"]
        E --> F[Data Agent & Metrics Engine]
        F -->|Strength Arithmetic| G[app/metrics/strength.py]
        F -->|Endurance Arithmetic| H[app/metrics/endurance.py]
        F -->|Muscle Classification| I[MuscleMapResolver]
    end

    subgraph LLM["4. AI Orchestration & Generation"]
        E --> J[Orchestrator Agent - Gemini 3.1 Flash Lite]
        J -->|Route Request| K[Strength / Endurance Analyst - Gemini 3.5 Flash]
        J -->|Visual Request| L[Chart Agent - Matplotlib 22 Presets]
        J -->|Q&A Context| M[Conversation Agent - 30-Min Window]
    end

    subgraph Output["5. Output Layer"]
        K & L & M --> N[Telegram HTML Renderer & Chunker]
        N -->|sendMessage / sendPhoto| O[User Telegram Chat]
    end
```

---

## ?? Quick Setup & Installation

### 1. Prerequisites
- **Python 3.12+** & **uv** (or standard `venv`)
- Credentials from **Telegram** (`@BotFather`), **Strava API**, **Google Gemini API**, **PostgreSQL** (Neon/Supabase), and **Upstash (Redis + QStash)**.

### 2. Clone & Install Environment
```bash
git clone https://github.com/nachiket0987/pulsefit-ai.git
cd pulsefit-ai

# Create Python 3.12 environment using uv
uv venv --python 3.12
source .venv/bin/activate    # On Windows: .venv\Scripts\activate

# Install dependencies
uv pip install -r requirements.txt
pytest -q                     # Run unit test suite (199 passed)
```

### 3. Environment Setup
Fill out `.env` with your API credentials:
```bash
cp .env.example .env   # Or edit the generated .env file
```

Auto-generate secret keys:
```bash
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"   # -> ENCRYPTION_KEY
python -c "import secrets; print(secrets.token_hex(32))"                                     # -> INTERNAL_API_SECRET
```

### 4. Database Migration & Webhook Binding
```bash
# Apply SQL Schema
python -c "from pathlib import Path; from app.config import get_settings; from app.storage.db import Database; Database(get_settings().database_url).apply_migrations(Path('migrations'))"

# Authenticate Strava OAuth & Webhooks
python scripts/bootstrap_oauth.py
python scripts/setup_strava_webhook.py create
python scripts/setup_telegram_webhook.py set
```

---

## ?? Deploying to Vercel

```bash
npm i -g vercel
vercel login
vercel                       # Deploy preview
vercel env add               # Bulk add .env keys to Vercel production
vercel --prod                # Production release
```

---

## ?? Supported Telegram Commands

| Command | Purpose |
|---|---|
| `/start` | Welcome & onboarding guide |
| `/last` | Resends the most recent workout briefing |
| `/week` | Displays summary for trailing 7 days |
| `/pr` | Queries personal records for specific exercises |
| `/chart` | Generates visual charts for exercise progress |
| `/compare` | Historical workout comparison |
| `/profile` | Views/updates training goals and physiology metrics |
| `/forget` | Clears active 30-minute session memory |

---

## ?? Author & Maintainer

**Nachiket Gadilohar**
- **Email**: [nachiketlohar0306@gmail.com](mailto:nachiketlohar0306@gmail.com)
- **GitHub**: [@nachiket0987](https://github.com/nachiket0987)
- **LinkedIn**: [linkedin.com/in/nachiket-gadilohar-profile](https://linkedin.com/in/nachiket-gadilohar-profile/)

---

## ?? License
Project developed for personal use and open portfolio demonstration.
