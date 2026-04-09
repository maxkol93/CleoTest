# Completion — ФинРоуст
**Version:** 1.0 | **Дата:** 2026-04-09

---

## 1. Deployment Plan

### 1.1 Environment Matrix

| Environment | Purpose | Infrastructure | URL |
|-------------|---------|---------------|-----|
| **dev** | Local development | Docker Compose on developer machine | localhost:8000 |
| **staging** | Pre-production testing | VPS (2 vCPU, 4GB) | staging.finroust.ru |
| **production** | Live service | VPS (4 vCPU, 8GB) — AdminVPS/HOSTKEY | api.finroust.ru |

### 1.2 Infrastructure Setup

```
Production VPS (AdminVPS/HOSTKEY):
├── OS: Ubuntu 22.04 LTS
├── CPU: 4 vCPU
├── RAM: 8 GB
├── Storage: 100 GB SSD (expandable)
├── Network: 100 Mbps, dedicated IPv4
├── Location: Moscow DC (152-ФЗ compliance)
│
├── Docker Engine 24+
├── Docker Compose v2
├── Nginx (TLS termination via Let's Encrypt)
├── UFW firewall (80, 443, 22 only)
└── Fail2ban (SSH protection)
```

### 1.3 Deployment Procedure

```
Pre-Deployment:
  1. [ ] All tests passing (unit + integration)
  2. [ ] Docker images built and tested locally
  3. [ ] Database migrations tested on staging
  4. [ ] Environment variables configured
  5. [ ] SSL certificates valid
  6. [ ] Backup created (DB + MinIO)

Deployment Steps:
  1. SSH to production VPS
  2. git pull origin main
  3. docker compose -f docker-compose.yml -f docker-compose.prod.yml pull
  4. docker compose run --rm api alembic upgrade head  # Run migrations
  5. docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
  6. docker compose ps  # Verify all services running
  7. curl -f https://api.finroust.ru/health  # Health check
  8. Check Grafana dashboards for anomalies (5 min)

Rollback Procedure:
  1. docker compose down
  2. git checkout <previous-commit>
  3. docker compose run --rm api alembic downgrade -1  # Rollback migration
  4. docker compose up -d
  5. Verify health check
  6. Post-mortem within 24 hours
```

### 1.4 Zero-Downtime Deploy (M6+)

```
Strategy: Rolling update with health checks

1. Scale API to 2 replicas: docker compose up -d --scale api=2
2. Deploy new version to replica 2
3. Health check replica 2
4. Switch Nginx upstream to replica 2
5. Update replica 1
6. Health check replica 1
7. Restore normal routing
```

---

## 2. CI/CD Pipeline

### 2.1 GitHub Actions Workflow

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install ruff mypy
      - run: ruff check src/
      - run: mypy src/ --ignore-missing-imports

  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env: { POSTGRES_DB: test, POSTGRES_PASSWORD: test }
        ports: ["5432:5432"]
      redis:
        image: redis:7-alpine
        ports: ["6379:6379"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install -r requirements.txt -r requirements-dev.txt
      - run: pytest tests/ -v --cov=src --cov-report=xml
      - uses: codecov/codecov-action@v4

  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - run: docker compose build
      - run: docker compose push  # To container registry

  deploy-staging:
    needs: [build]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to staging
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: deploy
          key: ${{ secrets.DEPLOY_KEY }}
          script: |
            cd /opt/finroust
            git pull origin main
            docker compose pull
            docker compose run --rm api alembic upgrade head
            docker compose up -d
            sleep 5
            curl -f http://localhost:8000/health

  deploy-production:
    needs: [deploy-staging]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production  # Requires manual approval
    steps:
      - name: Deploy to production
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.PROD_HOST }}
          username: deploy
          key: ${{ secrets.DEPLOY_KEY }}
          script: |
            cd /opt/finroust
            git pull origin main
            docker compose -f docker-compose.yml -f docker-compose.prod.yml pull
            docker compose run --rm api alembic upgrade head
            docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
            sleep 10
            curl -f https://api.finroust.ru/health
```

### 2.2 Branch Strategy

```
main ←── develop ←── feature/xxx
  │         │              │
  │         │         Development branches
  │         │
  │    Integration branch (staging deploys)
  │
  Production (auto-deploy after staging + manual approval)
```

---

## 3. Monitoring & Observability

### 3.1 Metrics (Prometheus)

**Application Metrics:**

| Metric | Type | Labels | Description |
|--------|------|--------|-------------|
| `bot_messages_total` | Counter | command, status | Total messages processed |
| `bot_response_time_seconds` | Histogram | command | Response latency |
| `ocr_processing_seconds` | Histogram | bank, status | OCR processing time |
| `llm_request_seconds` | Histogram | provider, status | LLM API latency |
| `llm_cost_total` | Counter | provider | LLM API cost tracking |
| `roasts_generated_total` | Counter | tone_mode, tier | Roasts generated |
| `roast_cards_shared_total` | Counter | — | Roast cards shared |
| `subscriptions_active` | Gauge | tier | Active paid subscriptions |
| `users_total` | Gauge | tier | Total users by tier |
| `payment_total` | Counter | status, provider | Payment events |

**Infrastructure Metrics:**
- Node exporter: CPU, RAM, disk, network
- PostgreSQL exporter: connections, queries/sec, replication lag
- Redis exporter: memory, commands/sec, connected clients

### 3.2 Logging (structlog → Loki)

```python
# Structured log format
{
    "timestamp": "2026-04-09T10:30:00Z",
    "level": "info",
    "event": "roast_generated",
    "user_id": "uuid",
    "telegram_id": 123456,
    "tone_mode": "ROAST",
    "roast_level": 3,
    "llm_provider": "openai",
    "latency_ms": 2340,
    "categories": ["food_delivery", "taxi", "subscriptions"]
}
```

**Log Levels:**
- `DEBUG`: Detailed flow tracing (dev only)
- `INFO`: Business events (roast generated, payment received, user created)
- `WARNING`: Degraded operations (LLM failover, OCR retry)
- `ERROR`: Failed operations (all LLMs down, payment failed)
- `CRITICAL`: System failures (DB down, out of memory)

### 3.3 Grafana Dashboards

| Dashboard | Panels | Purpose |
|-----------|--------|---------|
| **Bot Overview** | Messages/min, active users, response time p50/p99 | Real-time bot health |
| **LLM Performance** | Latency by provider, failover events, cost/hour | LLM provider health |
| **Business Metrics** | DAU/WAU/MAU, roasts/day, shares, conversions, MRR | Business KPIs |
| **Infrastructure** | CPU, RAM, disk, network, DB connections | Server health |
| **Errors** | Error rate, top error codes, error by endpoint | Error tracking |

### 3.4 Alerting Rules

| Alert | Condition | Severity | Channel |
|-------|-----------|:--------:|---------|
| API Down | Health check fails 3 consecutive times | Critical | Telegram admin + SMS |
| All LLMs Down | No successful LLM response in 5 min | Critical | Telegram admin + SMS |
| Error Rate High | 5xx rate > 5% for 5 min | Warning | Telegram admin |
| DB Connections | Active connections > 80% pool | Warning | Telegram admin |
| Disk Space | Usage > 85% | Warning | Telegram admin |
| Payment Failures | > 3 consecutive payment failures | Warning | Telegram admin |
| LLM Cost Spike | Cost > 2x daily average | Info | Telegram admin |
| User Spike | > 5x normal traffic in 10 min | Info | Telegram admin |

---

## 4. Backup & Recovery

### 4.1 Backup Strategy

| Component | Method | Frequency | Retention | Location |
|-----------|--------|:---------:|:---------:|----------|
| PostgreSQL | pg_dump (compressed) | Every 6 hours | 30 days | S3 (separate bucket) |
| PostgreSQL WAL | Continuous archiving | Continuous | 7 days | S3 |
| Redis | RDB snapshot | Every hour | 24 hours | Local + S3 |
| MinIO (images) | rsync to backup storage | Daily | 90 days | Separate VPS |
| Config files | Git repository | On change | Indefinite | GitHub |
| .env secrets | Encrypted backup | On change | Indefinite | Secure storage |

### 4.2 Recovery Procedures

```
Scenario: Database corruption
  1. Stop API and worker services
  2. pg_restore from latest backup
  3. Apply WAL logs for point-in-time recovery
  4. Verify data integrity
  5. Restart services
  RTO: 30 minutes | RPO: 6 hours (worst case)

Scenario: Complete VPS failure
  1. Provision new VPS (AdminVPS/HOSTKEY)
  2. Install Docker + Docker Compose
  3. Pull latest code from GitHub
  4. Restore PostgreSQL from S3 backup
  5. Restore MinIO from backup VPS
  6. Configure .env from secure storage
  7. docker compose up -d
  8. Update DNS records
  9. Verify health checks
  RTO: 1 hour | RPO: 6 hours

Scenario: Accidental data deletion
  1. Identify affected data and timeframe
  2. Restore PostgreSQL to point-in-time (WAL)
  3. Merge restored data with current (if partial)
  4. Notify affected users
  RTO: 1 hour | RPO: 15 minutes (WAL)
```

---

## 5. Operational Runbooks

### 5.1 Common Operations

```
# View logs
docker compose logs -f bot --tail=100
docker compose logs -f api --tail=100
docker compose logs -f worker --tail=100

# Restart single service
docker compose restart bot

# Run database migration
docker compose run --rm api alembic upgrade head

# Rollback last migration
docker compose run --rm api alembic downgrade -1

# Access database
docker compose exec db psql -U finroust -d finroust

# Clear Redis cache
docker compose exec redis redis-cli FLUSHDB

# Seed categories
docker compose run --rm api python scripts/seed_categories.py

# Check service health
curl -f https://api.finroust.ru/health
docker compose ps
```

### 5.2 Incident Response

```
Level 1 (Info): Degraded performance
  1. Check Grafana dashboards
  2. Identify bottleneck (LLM? DB? CPU?)
  3. Scale if needed (add worker, increase pool)
  4. Monitor for 30 min

Level 2 (Warning): Service partially down
  1. Check docker compose ps for failed containers
  2. Check logs for errors
  3. Restart failed service
  4. If DB issue: check connections, restart if needed
  5. If LLM issue: verify failover is working
  6. Post-mortem if recovery > 15 min

Level 3 (Critical): Service fully down
  1. Alert team via Telegram + SMS
  2. SSH to VPS, check system resources
  3. docker compose down && docker compose up -d
  4. If VPS unresponsive: contact hosting provider
  5. If data loss suspected: begin recovery procedure
  6. Post-mortem required within 24 hours
```

---

## 6. Launch Checklist

### 6.1 Pre-Launch (M1)

```
Infrastructure:
  [ ] VPS provisioned (AdminVPS/HOSTKEY, Moscow DC)
  [ ] Docker + Docker Compose installed
  [ ] Nginx configured with Let's Encrypt SSL
  [ ] Firewall configured (UFW: 80, 443, 22)
  [ ] Fail2ban configured
  [ ] Monitoring stack deployed (Prometheus + Grafana + Loki)
  [ ] Backup cron jobs configured

Application:
  [ ] All Docker images built and tested
  [ ] Database migrations run
  [ ] Categories seeded
  [ ] Telegram bot registered with @BotFather
  [ ] Webhook URL configured
  [ ] LLM API keys configured and tested
  [ ] Payment provider (YooKassa) configured
  [ ] MinIO configured with access keys
  [ ] All environment variables set

Testing:
  [ ] Unit tests passing (>80% coverage)
  [ ] Integration tests passing
  [ ] E2E test: /start → upload → roast → share flow
  [ ] Payment flow tested in sandbox
  [ ] OCR tested with real screenshots from top-3 banks
  [ ] LLM failover tested

Legal:
  [ ] 152-ФЗ consent text approved
  [ ] Privacy policy published
  [ ] Terms of service published
  [ ] Financial disclaimer text approved

Launch:
  [ ] Deploy to production
  [ ] Health check green
  [ ] Invite beta testers (50-100 users)
  [ ] Monitor for 48 hours
  [ ] Fix critical issues
  [ ] Open for wider access
```

### 6.2 Post-Launch (Week 1)

```
Daily:
  [ ] Check Grafana dashboards
  [ ] Review error logs
  [ ] Respond to user feedback
  [ ] Monitor LLM costs

Weekly:
  [ ] Analyze conversion funnel
  [ ] Review OCR accuracy by bank
  [ ] A/B test roast_level defaults
  [ ] Community feedback analysis
```

---

## 7. Scaling Plan

| Phase | Users | Infrastructure Changes |
|-------|:-----:|----------------------|
| M1-3 | 2K | 1 VPS (4 vCPU, 8GB), single PostgreSQL |
| M4-6 | 25K | Upgrade VPS (8 vCPU, 16GB), add Redis sentinel |
| M7-9 | 50K | Add DB read replica, 2 worker instances |
| M10-12 | 100K | Add second VPS for workers, DB connection pooling (PgBouncer) |
| M13-18 | 200K | 3 VPS cluster, PostgreSQL partitioning, CDN for static |
| M19-24 | 320K | Evaluate migration to managed infrastructure if needed |

---

## 8. Documentation Handoff

| Document | Location | Purpose |
|----------|----------|---------|
| PRD | `docs/PRD.md` | Product requirements |
| Specification | `docs/Specification.md` | User stories + acceptance criteria |
| Pseudocode | `docs/Pseudocode.md` | Algorithms + data structures |
| Architecture | `docs/Architecture.md` | System design + tech stack |
| Refinement | `docs/Refinement.md` | Edge cases + tests |
| Completion | `docs/Completion.md` | This document |
| Research | `docs/Research_Findings.md` | Market + competitive research |
| Strategy | `docs/Solution_Strategy.md` | Business + technical strategy |
| ADRs | `docs/adr/ADR-*.md` | Architecture decisions |
| CJM | `docs/CJM_Variants.md` | Customer journey maps |
| Development Guide | `DEVELOPMENT_GUIDE.md` | Developer onboarding |
| AI Assistant Context | `CLAUDE.md` | For Claude Code / AI tools |
