# BUSINESS MODEL & FINANCE: AI Финансовый Коуч → Россия
**Режим:** DEEP | **Дата:** 2026-04-08

---

## A. REVENUE MODEL

**Тип:** Freemium SaaS (подписка через Telegram)

| Tier | Цена | Что включено | Target Segment |
|------|------|-------------|----------------|
| Free | 0₽ | 1 roast/неделю, базовая категоризация, top-3 расходов | Все (awareness + viral) |
| Pro | 399₽/мес (3,499₽/год) | Безлимит roasts, план экономии, подписки-детектор, challenges, pie-charts | Broke but Self-Aware + Aspiring Saver |
| Premium | 799₽/мес (6,999₽/год) | + AI-коуч с памятью, голосовые, кредитный скоринг, приоритетная поддержка | Power users |

**Дополнительные revenue streams (Phase 2+):**
1. B2B white-label для банков/финтех — продажа AI personality engine [H]
2. Affiliate: рекомендации финансовых продуктов (вклады, карты) — CPA модель [H]

---

## B. UNIT ECONOMICS

| Метрика | Cleo (US) | Benchmark Fintech | Наш аналог (plan) | Источник | Confidence |
|---------|-----------|-------------------|-------------------|----------|:----------:|
| **ARPU** (monthly) | ~$17 | $10-30 | 399₽ ($4.5) → 480₽ ($5.5) blended | [Sacra](https://sacra.com/c/cleo/), [industry benchmarks](./M1_Fact_Sheet.md) | 0.85 |
| **CAC** | $5-15 [H] | $100-500 (consumer fintech) | 150-300₽ ($1.7-3.5) | Telegram viral = low CAC. [FinMKT benchmarks](https://www.finmkt.io/blog-posts/embedded-finance-trends-2025) | 0.70 |
| **LTV** | ~$200 [H] | $500-2000 | 4,300₽ ($49) | расчёт: ARPU × (1/churn) × margin | 0.65 |
| **LTV:CAC** | ~13:1 [H] | 3:1 - 5:1 | **14-28:1** | расчёт | 0.65 |
| **Payback** | ~1 мес [H] | 12-15 мес | **<1 мес** | расчёт: CAC / (ARPU × margin) | 0.65 |
| **Gross Margin** | ~75% [H] | 50-75% (fintech) | **80%** | Чистый SaaS, нет lending costs | 0.80 |
| **Monthly Churn** | ~5% [H] | 5-7% (SMB SaaS) | **7%** (conservative) | [Recurly benchmarks](https://www.databridgemarketresearch.com/reports/global-personal-finance-management-market) | 0.70 |

**Формулы:**
- LTV = 480₽ × (1/0.07) × 0.80 = 480 × 14.3 × 0.80 = **5,486₽** (~$63)
- Payback = 250₽ / (480₽ × 0.80) = 0.65 мес = **~20 дней**
- LTV:CAC = 5,486 / 250 = **22:1** (excellent)

**⚠️ Note:** LTV:CAC > 10:1 выглядит слишком хорошо → likely CAC underestimated. При CAC 500₽ → LTV:CAC = 11:1 (всё ещё отлично).

---

## C. P&L PROJECTION (24 месяца, Россия)

### Допущения

| Допущение | Значение | Тип | Обоснование |
|-----------|----------|:---:|-------------|
| Users M1 | 2,000 | [H] | Beta launch через Telegram каналы |
| MoM рост | 25% (M1-6), 15% (M7-12), 10% (M13-24) | [H] | Viral product benchmark |
| Конверсия Free→Paid | 6% | [B] | Industry 3-8%, мы в середине |
| ARPU (blended) | 480₽/мес | [F] | 70% Pro (399₽) + 30% Premium (799₽) |
| CAC | 250₽ | [H] | Telegram viral + content marketing |
| Команда (M1) | 3 чел | [H] | 1 fullstack + 1 ML/AI + 1 growth |
| Зарплата avg | 250,000₽/мес | [F] | Moscow senior dev avg — [worldsalaries](https://worldsalaries.com/average-salary-in-russia/) |
| Infra/LLM costs | 50,000₽/мес base + per-user | [H] | VPS + LLM API |

### Модель

| | M1 | M3 | M6 | M9 | M12 | M18 | M24 |
|---|---|---|---|---|---|---|---|
| **Total Users** | 2,000 | 6,250 | 24,400 | 55,900 | 98,400 | 192,000 | 320,000 |
| **Paying Users** | 120 | 375 | 1,464 | 3,354 | 5,904 | 11,520 | 19,200 |
| **MRR** | 57,600₽ | 180,000₽ | 702,720₽ | 1,610K₽ | 2,834K₽ | 5,530K₽ | 9,216K₽ |
| COGS (LLM + infra) | 20,000₽ | 40,000₽ | 140,000₽ | 320,000₽ | 570,000₽ | 1,100K₽ | 1,840K₽ |
| **Gross Profit** | 37,600₽ | 140,000₽ | 562,720₽ | 1,290K₽ | 2,264K₽ | 4,430K₽ | 7,376K₽ |
| Marketing | 30,000₽ | 93,750₽ | 150,000₽ | 300,000₽ | 500,000₽ | 700,000₽ | 900,000₽ |
| Team (3→5→7) | 750,000₽ | 750,000₽ | 1,000,000₽ | 1,250,000₽ | 1,250,000₽ | 1,500,000₽ | 1,750,000₽ |
| Other OpEx | 50,000₽ | 50,000₽ | 80,000₽ | 100,000₽ | 120,000₽ | 150,000₽ | 200,000₽ |
| **Total OpEx** | 830,000₽ | 893,750₽ | 1,230,000₽ | 1,650,000₽ | 1,870,000₽ | 2,350,000₽ | 2,850,000₽ |
| **Net P&L** | **-792,400₽** | **-753,750₽** | **-667,280₽** | **-360,000₽** | **+394,000₽** | **+2,080K₽** | **+4,526K₽** |
| **Cumulative** | -792K₽ | -2,300K₽ | -4,800K₽ | -5,800K₽ | **-4,200K₽** | **+2,500K₽** | **+15,000K₽** |

---

## D. RESOLVED CONTRADICTIONS (TRIZ)

| # | Противоречие | Тип | TRIZ Principle | Решение |
|---|-------------|-----|----------------|---------|
| 1 | Цена: высокая для unit economics, низкая для РФ рынка | Physical | Separation in Time | Freemium → pro after proof-of-value. 399₽ = 4.6% от экономии 8,700₽ |
| 2 | Команда: большая для скорости, маленькая для runway | Physical | Separation in Space | Core team 3 чел + AI automation (LLM делает работу 10 человек) |
| 3 | CAC: низкий для economics, quality для LTV | Technical | #23 Feedback | Viral roast → organic users = low CAC + high-intent (they came for fun) |
| 4 | Deep bank integration vs Open API недоступен | Technical | #13 Other Way Round | Screenshot OCR вместо API. User приносит данные сам |

---

## E. FUNDING ROADMAP

| Раунд | Когда | Сколько | На что | KPIs | Valuation est. |
|-------|-------|---------|--------|------|:--------------:|
| Bootstrap | M0 | 0₽ (свои) | MVP Telegram bot | Идея + команда | — |
| Friends & Family | M1-2 | 1-2M₽ | Beta + content | 2K users, 100 paying | 10-20M₽ |
| Angel/Pre-seed | M4-6 | 5-10M₽ | Growth + team | 20K users, 1K paying, 400K₽ MRR | 50-100M₽ |
| Seed | M10-14 | 30-50M₽ | Scale + features | 100K users, 5K paying, 2.5M₽ MRR | 300-500M₽ |
| Series A | M20-24 | 150-300M₽ | Expansion + mobile app | 300K users, 15K paying, 7M₽ MRR | 1-2B₽ |

---

## F. BREAK-EVEN

| Сценарий | Платящие | MRR | Месяц | Вероятность |
|----------|---------|-----|:-----:|:-----------:|
| 🟢 Оптимистичный | 4,500 | 2,160K₽ | M10 | 20% |
| 🟡 Реалистичный | 5,900 | 2,832K₽ | M12 | 60% |
| 🔴 Пессимистичный | 8,000 | 3,840K₽ | M16 | 20% |

---

## G. SENSITIVITY

| Изменение | Break-even | LTV:CAC | Verdict |
|-----------|:----------:|:-------:|---------|
| CAC +100% (500₽) | +2 мес (M14) | 11:1 | ✅ Приемлемо |
| Churn +3% (10%) | +3 мес (M15) | 15:1 | ✅ Приемлемо |
| ARPU -25% (360₽) | +4 мес (M16) | 16:1 | ⚠️ На грани |
| Конверсия -50% (3%) | +8 мес (M20) | 22:1 (same) | 🔴 Критично — нужен pivot на monetization |
| Всё вместе worst case | M22+ | 5:1 | 🔴 Требует funding |

**Ключевой риск:** Конверсия Free→Paid. Если <3% — модель не работает без изменения стратегии.

---

## Confidence Summary

| Блок | Avg Confidence | Min |
|------|:--------------:|:---:|
| Revenue Model | 0.85 | 0.80 |
| Unit Economics | 0.72 | 0.65 |
| P&L Projection | 0.68 | 0.65 |
| Funding Roadmap | 0.60 | 0.55 |
| Break-Even | 0.70 | 0.65 |
| **ИТОГО** | **0.71** | **0.55** |

## ⚠️ Рисковые допущения
- [ ] CAC 250₽ — может быть 2-3x выше при масштабировании
- [ ] Конверсия 6% — нужно валидировать на MVP (может быть 2-8%)
- [ ] Churn 7% — Russian subscription churn может быть выше (10-12%) для young audience
- [ ] ARPU 480₽ blended — зависит от Pro/Premium mix
