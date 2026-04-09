# Architecture — ФинРоуст
**Version:** 1.0 | **Дата:** 2026-04-09

---

## 1. Architecture Overview

### Pattern: Distributed Monolith (Monorepo)

Все сервисы в одном репозитории, деплоятся как отдельные Docker-контейнеры через Docker Compose на VPS.

```
┌──────────────────────────────────────────────────────────┐
│                        VPS Host                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │                  Docker Compose                    │  │
│  │                                                    │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │  │
│  │  │ Telegram  │  │   API    │  │   Worker          │ │  │
│  │  │   Bot     │→ │  Server  │→ │  (Background)     │ │  │
│  │  │ (webhook) │  │ (FastAPI)│  │  (Celery/ARQ)     │ │  │
│  │  └──────────┘  └────┬─────┘  └────────┬───────────┘ │  │
│  │                     │                  │             │  │
│  │              ┌──────┴──────┐    ┌──────┴──────┐     │  │
│  │              │ PostgreSQL  │    │    Redis     │     │  │
│  │              │   (data)    │    │ (cache/queue)│     │  │
│  │              └─────────────┘    └─────────────┘     │  │
│  │                                                    │  │
│  │  ┌──────────────┐  ┌────────────────────────────┐  │  │
│  │  │ MinIO (S3)   │  │  Nginx (reverse proxy)     │  │  │
│  │  │ (images/files)│  │  + TLS termination         │  │  │
│  │  └──────────────┘  └────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  External APIs:                                          │
│  ┌─────────┐ ┌─────────┐ ┌──────────┐ ┌──────────────┐ │
│  │ OpenAI  │ │ Claude  │ │ YandexGPT│ │ YooKassa     │ │
│  │ GPT-4o  │ │ (fallb) │ │ (fallb)  │ │ (payments)   │ │
│  └─────────┘ └─────────┘ └──────────┘ └──────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### Architecture Constraints (from Pipeline)

| Constraint | Value |
|-----------|-------|
| Pattern | Distributed Monolith (Monorepo) |
| Containers | Docker + Docker Compose |
| Infrastructure | VPS (AdminVPS/HOSTKEY) |
| Deploy | Docker Compose direct deploy |
| AI Integration | MCP servers |

---

## 2. Component Diagram (C4 Level 2)

### System Context

```
┌─────────────────────┐
│   Telegram User      │
│   (18-30, Gen Z)     │
└──────────┬──────────┘
           │ Telegram Bot API
           ▼
┌──────────────────────┐
│   ФинРоуст System    │
│   (AI Finance Coach) │
└──────────┬──────────┘
           │
     ┌─────┼─────┬──────────┐
     ▼     ▼     ▼          ▼
┌───────┐┌────┐┌────────┐┌────────┐
│OpenAI ││OCR ││YooKassa││MinIO   │
│Claude ││Svc ││Payment ││Storage │
│Yandex ││    ││        ││        │
└───────┘└────┘└────────┘└────────┘
```

### Container Diagram

| Container | Technology | Purpose | Port |
|-----------|-----------|---------|:----:|
| **bot** | Python 3.12 + python-telegram-bot | Telegram webhook handler, command routing | 8443 |
| **api** | Python 3.12 + FastAPI | REST API, business logic, LLM orchestration | 8000 |
| **worker** | Python 3.12 + ARQ (Redis-based) | Background jobs: OCR, image gen, cron tasks | — |
| **db** | PostgreSQL 16 | Primary data store | 5432 |
| **redis** | Redis 7 | Cache, task queue, rate limiting, sessions | 6379 |
| **minio** | MinIO | S3-compatible object storage (images, files) | 9000 |
| **nginx** | Nginx 1.25 | Reverse proxy, TLS termination, static files | 80/443 |

---

## 3. Technology Stack

### Backend

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Language | **Python 3.12** | Best ML/AI ecosystem, LLM libraries, OCR tools |
| Web Framework | **FastAPI** | Async, fast, auto-docs, type hints |
| Telegram SDK | **python-telegram-bot 21.x** | Official, well-maintained, async |
| ORM | **SQLAlchemy 2.0 + Alembic** | Async support, mature, migration tool |
| Task Queue | **ARQ** (Redis-based) | Lightweight, async, Python-native |
| OCR | **Tesseract 5 + pytesseract** | Self-hosted, free, good Russian support |
| Image Generation | **Pillow (PIL)** | Lightweight, no external deps for card generation |
| LLM Client | **openai SDK + anthropic SDK + custom** | Official SDKs + custom wrapper for YandexGPT/GigaChat |
| Validation | **Pydantic v2** | FastAPI native, data validation |
| Testing | **pytest + pytest-asyncio** | Standard, async support |

### Infrastructure

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Container Runtime | **Docker 24+** | Industry standard |
| Orchestration | **Docker Compose v2** | Simple, fits VPS deploy |
| Database | **PostgreSQL 16** | JSONB for flexible schemas, proven reliability |
| Cache/Queue | **Redis 7** | Cache, task queue, rate limiting, pub/sub |
| Object Storage | **MinIO** | S3-compatible, self-hosted, free |
| Reverse Proxy | **Nginx** | TLS, rate limiting, static serving |
| VPS | **AdminVPS/HOSTKEY** | Russian DC, 152-ФЗ compliance |
| Monitoring | **Prometheus + Grafana** | Open-source, Docker-native |
| Logging | **structlog + Loki** | Structured JSON logs, Grafana integration |

### External Services

| Service | Provider | Purpose | Fallback |
|---------|---------|---------|----------|
| LLM (primary) | OpenAI GPT-4o | Roast generation, categorization | Claude |
| LLM (fallback 1) | Anthropic Claude | Fallback if GPT-4o unavailable | YandexGPT |
| LLM (fallback 2) | Yandex YandexGPT | Russian provider, no sanctions risk | GigaChat |
| LLM (fallback 3) | Sber GigaChat | Last resort | — |
| OCR (fallback) | Yandex Vision API | If Tesseract accuracy insufficient | — |
| Payments | YooKassa | Subscription payments | CloudPayments |
| Bot Platform | Telegram Bot API | Primary delivery channel | — |

---

## 4. Data Architecture

### Database Schema (PostgreSQL)

```sql
-- Core tables
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    telegram_id BIGINT UNIQUE NOT NULL,
    username VARCHAR(255),
    display_name VARCHAR(255) NOT NULL,
    tier VARCHAR(20) DEFAULT 'FREE' CHECK (tier IN ('FREE', 'PRO', 'PREMIUM')),
    roast_level INT DEFAULT 3 CHECK (roast_level BETWEEN 1 AND 5),
    tone_mode VARCHAR(20) DEFAULT 'ROAST' CHECK (tone_mode IN ('ROAST', 'HYPE', 'SERIOUS')),
    onboarding_state VARCHAR(20) DEFAULT 'NEW',
    locale VARCHAR(10) DEFAULT 'ru',
    timezone VARCHAR(50) DEFAULT 'Europe/Moscow',
    streak_days INT DEFAULT 0,
    streak_last_date DATE,
    last_activity_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    deleted_at TIMESTAMP WITH TIME ZONE
);

CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) UNIQUE NOT NULL,
    emoji VARCHAR(10) NOT NULL,
    parent_id UUID REFERENCES categories(id),
    sort_order INT DEFAULT 0
);

CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    amount DECIMAL(12,2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'RUB',
    description TEXT NOT NULL,
    category_id UUID REFERENCES categories(id),
    category_confidence FLOAT,
    source VARCHAR(20) NOT NULL CHECK (source IN ('OCR', 'CSV', 'MANUAL')),
    original_text TEXT,
    transaction_date DATE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE subscriptions_detected (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    merchant VARCHAR(255) NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    frequency VARCHAR(20) CHECK (frequency IN ('WEEKLY', 'MONTHLY', 'YEARLY')),
    status VARCHAR(20) DEFAULT 'ACTIVE',
    detected_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_charge_date DATE
);

CREATE TABLE challenges (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    challenge_type VARCHAR(50) NOT NULL,
    description TEXT NOT NULL,
    target_value DECIMAL(12,2),
    current_value DECIMAL(12,2) DEFAULT 0,
    status VARCHAR(20) DEFAULT 'ACTIVE' CHECK (status IN ('ACTIVE', 'COMPLETED', 'FAILED', 'SKIPPED')),
    started_at DATE NOT NULL,
    ends_at DATE NOT NULL
);

CREATE TABLE roast_cards (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    roast_text TEXT NOT NULL,
    image_url TEXT,
    hide_amounts BOOLEAN DEFAULT FALSE,
    share_count INT DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE user_subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    tier VARCHAR(20) NOT NULL CHECK (tier IN ('PRO', 'PREMIUM')),
    payment_provider VARCHAR(50) NOT NULL,
    payment_id VARCHAR(255),
    started_at TIMESTAMP WITH TIME ZONE NOT NULL,
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    auto_renew BOOLEAN DEFAULT TRUE,
    cancelled_at TIMESTAMP WITH TIME ZONE
);

CREATE TABLE badges (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    badge_type VARCHAR(50) NOT NULL,
    earned_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(user_id, badge_type)
);

-- Indexes
CREATE INDEX idx_transactions_user_date ON transactions(user_id, transaction_date DESC);
CREATE INDEX idx_transactions_category ON transactions(category_id);
CREATE INDEX idx_users_telegram ON users(telegram_id);
CREATE INDEX idx_challenges_user_active ON challenges(user_id, status) WHERE status = 'ACTIVE';
CREATE INDEX idx_user_subs_user ON user_subscriptions(user_id, expires_at DESC);
```

### Redis Key Schema

```
# Rate limiting
rate:{telegram_id}:messages → counter (TTL: 60s)
rate:{telegram_id}:uploads → counter (TTL: 3600s)

# Cache
cache:user:{telegram_id} → JSON user object (TTL: 300s)
cache:categories → JSON category list (TTL: 3600s)

# Session/state
state:{telegram_id}:onboarding → onboarding step
state:{telegram_id}:upload → upload flow state

# Counters
counter:roasts:{telegram_id}:week:{week_number} → int (TTL: 7d)

# Task queue (ARQ managed)
arq:queue:default → task queue
arq:queue:ocr → OCR processing queue
arq:queue:llm → LLM generation queue
```

---

## 5. LLM Abstraction Layer

```
┌─────────────────────────────────────────────┐
│              LLM Router                      │
│  ┌─────────────────────────────────────────┐ │
│  │ Priority Chain:                          │ │
│  │ 1. GPT-4o (primary)                     │ │
│  │ 2. Claude Sonnet (fallback #1)          │ │
│  │ 3. YandexGPT (fallback #2, Russian)     │ │
│  │ 4. GigaChat (fallback #3, Russian)      │ │
│  └─────────────────────────────────────────┘ │
│                                              │
│  Features:                                   │
│  - Auto-failover on error/timeout            │
│  - Rate limiting per provider                │
│  - Cost tracking per request                 │
│  - Latency monitoring                        │
│  - Response caching (Redis, similar prompts) │
└─────────────────────────────────────────────┘
```

### Provider Configuration

| Provider | Model | Max Tokens | Temperature | Timeout | Cost/1K tokens |
|----------|-------|:----------:|:-----------:|:-------:|:--------------:|
| OpenAI | gpt-4o | 500 | 0.8 | 15s | ~$0.005 |
| Anthropic | claude-sonnet-4-6 | 500 | 0.8 | 15s | ~$0.003 |
| Yandex | yandexgpt/latest | 500 | 0.8 | 20s | ~₽0.12 |
| Sber | GigaChat-Pro | 500 | 0.8 | 20s | ~₽0.10 |

### Prompt Architecture

```
System Prompt (static per tone_mode):
├── Base personality ("ФинРоуст — дерзкий друг...")
├── Tone modifier (roast_level 1-5 OR hype OR serious)
├── Safety rules ("НЕ оскорблять личность, НЕ нецензурно...")
└── Format rules ("100-300 символов, 1 совет в конце...")

User Prompt (dynamic per request):
├── Aggregated spending data (categories + amounts)
├── Insights (biggest expense, trends, subscriptions)
├── Context (period, comparison with previous)
└── Task instruction ("Сгенерируй roast/hype/analysis")

⚠️ PII Protection:
- NEVER send raw transaction descriptions to LLM
- Only send: category names + aggregated amounts
- User names/IDs never included in prompts
```

---

## 6. Security Architecture

### Data Flow Security

```
User (Telegram) → [TLS 1.3] → Telegram Servers → [TLS 1.3] → Nginx → [internal] → Bot/API
                                                                         ↓
                                                                   PostgreSQL [AES-256]
                                                                   Redis [password auth]
                                                                   MinIO [TLS + access keys]
```

### 152-ФЗ Compliance

| Requirement | Implementation |
|-------------|----------------|
| Data localization | All servers in Russian DC (AdminVPS/HOSTKEY) |
| User consent | Explicit consent on /start (inline button "Согласен") |
| Data access request | /export command → CSV download |
| Data deletion | /delete command → full data wipe |
| Data minimization | Only store transaction data needed for core features |
| PII in LLM | NEVER send raw PII to external LLM APIs |
| Encryption at rest | PostgreSQL TDE, disk encryption on VPS |
| Encryption in transit | TLS 1.3 everywhere |
| Audit log | All data access/modification logged with timestamps |

### Rate Limiting

| Endpoint | Limit | Window | Action on Exceed |
|----------|:-----:|:------:|------------------|
| Messages (any) | 60 | 1 min | "Слишком часто. Подожди минутку" |
| Photo uploads | 10 | 1 hour | "Лимит загрузок. Попробуй через час" |
| /roast (Free) | 1 | 1 week | Upsell to Pro |
| /roast (Pro) | 50 | 1 day | "Даже для Pro это многовато" |
| Payment webhooks | 100 | 1 min | Log + alert |

---

## 7. Deployment Architecture

### Docker Compose Services

```yaml
# Simplified service map
services:
  nginx:        # Reverse proxy + TLS
    ports: ["80:80", "443:443"]
    depends_on: [bot, api]

  bot:          # Telegram webhook handler
    build: ./src/bot
    depends_on: [db, redis, api]
    env: [TELEGRAM_TOKEN, API_URL]

  api:          # Business logic API
    build: ./src/api
    depends_on: [db, redis, minio]
    env: [DATABASE_URL, REDIS_URL, LLM_KEYS, MINIO_URL]

  worker:       # Background task processor
    build: ./src/worker
    depends_on: [db, redis, minio]
    env: [DATABASE_URL, REDIS_URL, LLM_KEYS]

  db:           # PostgreSQL
    image: postgres:16
    volumes: [pgdata:/var/lib/postgresql/data]

  redis:        # Redis
    image: redis:7-alpine

  minio:        # Object storage
    image: minio/minio
    volumes: [miniodata:/data]
```

### Deployment Pipeline

```
Developer → git push → GitHub Actions CI:
  1. Lint (ruff, mypy)
  2. Unit tests (pytest)
  3. Build Docker images
  4. Push to container registry
  5. SSH to VPS → docker compose pull && docker compose up -d
  6. Health check → rollback if failed
```

### Monitoring Stack

```
┌──────────────────────────────────┐
│           Grafana                │
│  ┌──────────┐  ┌──────────────┐ │
│  │ Prometheus│  │     Loki     │ │
│  │ (metrics) │  │   (logs)     │ │
│  └─────┬────┘  └──────┬───────┘ │
│        │               │         │
│   ┌────┴────┐    ┌─────┴────┐   │
│   │ Bot/API │    │ structlog│   │
│   │ /metrics│    │ → stdout │   │
│   └─────────┘    └──────────┘   │
└──────────────────────────────────┘

Key Dashboards:
- Bot: messages/sec, response time, errors
- LLM: latency by provider, failover events, cost/day
- Business: DAU, roasts/day, shares, conversions
- Infra: CPU, RAM, disk, DB connections
```

### Alerting

| Alert | Condition | Channel | Severity |
|-------|-----------|---------|:--------:|
| API down | Health check fails 3x | Telegram admin group | Critical |
| All LLMs down | All 4 providers fail in 5 min | Telegram + SMS | Critical |
| DB connection pool exhausted | Active connections > 80% | Telegram admin group | Warning |
| Error rate spike | 5xx > 5% in 5 min | Telegram admin group | Warning |
| Disk space | > 85% used | Telegram admin group | Warning |
| Slow response | p99 > 10s for 5 min | Telegram admin group | Info |

---

## 8. Monorepo Structure

```
finroust/
├── docker-compose.yml          # Service orchestration
├── docker-compose.prod.yml     # Production overrides
├── Dockerfile                  # Multi-stage build
├── .env.example                # Environment template
├── .gitignore
├── README.md
├── CLAUDE.md                   # AI assistant context
├── DEVELOPMENT_GUIDE.md        # Dev setup guide
│
├── src/
│   ├── shared/                 # Shared code (models, utils)
│   │   ├── models/             # SQLAlchemy models
│   │   ├── schemas/            # Pydantic schemas
│   │   ├── services/           # Business logic services
│   │   │   ├── llm_provider.py     # LLM abstraction layer
│   │   │   ├── ocr_service.py      # OCR pipeline
│   │   │   ├── categorizer.py      # Transaction categorization
│   │   │   ├── roast_engine.py     # Roast generation
│   │   │   ├── card_generator.py   # Image card generation
│   │   │   ├── subscription_detector.py
│   │   │   ├── challenge_engine.py
│   │   │   └── payment_service.py
│   │   ├── config.py           # Settings (Pydantic BaseSettings)
│   │   └── database.py         # DB connection
│   │
│   ├── bot/                    # Telegram bot
│   │   ├── handlers/           # Command handlers
│   │   │   ├── start.py
│   │   │   ├── roast.py
│   │   │   ├── upload.py
│   │   │   ├── stats.py
│   │   │   ├── settings.py
│   │   │   ├── subscription.py
│   │   │   └── privacy.py
│   │   ├── keyboards/          # Inline keyboards
│   │   ├── middleware/          # Rate limiting, auth
│   │   └── main.py             # Bot entrypoint
│   │
│   ├── api/                    # FastAPI server
│   │   ├── routes/             # API endpoints
│   │   │   ├── webhook.py      # Telegram webhook
│   │   │   ├── payment.py      # Payment webhooks
│   │   │   └── health.py       # Health checks
│   │   ├── middleware/
│   │   └── main.py             # API entrypoint
│   │
│   └── worker/                 # Background workers
│       ├── tasks/
│       │   ├── ocr_task.py
│       │   ├── roast_task.py
│       │   ├── card_task.py
│       │   └── cron_tasks.py   # Weekly report, challenges, churn
│       └── main.py             # Worker entrypoint
│
├── migrations/                 # Alembic migrations
│   ├── alembic.ini
│   └── versions/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
│
├── scripts/
│   ├── seed_categories.py      # Seed category data
│   └── migrate.sh              # Run migrations
│
├── config/
│   ├── nginx/                  # Nginx config
│   ├── prometheus/             # Prometheus config
│   └── grafana/                # Grafana dashboards
│
├── docs/                       # SPARC documentation
│   ├── PRD.md
│   ├── Specification.md
│   ├── Pseudocode.md
│   ├── Architecture.md         # This file
│   ├── Refinement.md
│   ├── Completion.md
│   └── adr/                    # Architecture Decision Records
│
└── .claude/                    # Claude Code toolkit
    ├── commands/
    ├── agents/
    ├── rules/
    └── skills/
```

---

## 9. ADR Index

| ADR | Decision | Status |
|-----|----------|--------|
| [ADR-001](adr/ADR-001-platform-choice.md) | Telegram-first platform | Accepted |
| [ADR-002](adr/ADR-002-aha-moment.md) | Roast Mode as core Aha Moment | Accepted |
| [ADR-003](adr/ADR-003-monetization-model.md) | Freemium with proof-of-value paywall | Accepted |
| [ADR-004](adr/ADR-004-ai-personality.md) | "Дерзкий друг" persona with tone switching | Accepted |
| [ADR-005](adr/ADR-005-data-input-method.md) | Screenshot OCR + CSV first | Accepted |
| [ADR-006](adr/ADR-006-llm-provider.md) | Multi-LLM provider with failover | Accepted |
| ADR-007 | Python + FastAPI + SQLAlchemy stack | Accepted (this document) |
| ADR-008 | Docker Compose deploy on VPS | Accepted (this document) |
| ADR-009 | ARQ for background tasks (over Celery) | Accepted (this document) |
