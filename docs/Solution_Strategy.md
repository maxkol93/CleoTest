# Solution Strategy — ФинРоуст
**Дата:** 2026-04-09

---

## 1. Стратегическая Позиция

### Problem-Solution Fit

| Problem | Solution | Evidence |
|---------|----------|----------|
| PFM apps = скучные (Дзен-мани, CoinKeeper) | AI Personality: Roast/Hype/Serious modes | Cleo: Roast Mode = #1 differentiator, 20+ упоминаний в отзывах |
| Open Banking РФ недоступен до 2027 | Screenshot OCR + CSV input | TRIZ #13: "Other Way Round" — user приносит данные сам |
| Gen Z не ведут бюджет (91% в digital, <5% budgeting) | Gamification + viral mechanics | 62% Gen Z open to AI finance ([PYMNTS](https://www.pymnts.com/credit-unions/2025/62percent-of-gen-z-turn-to-ai-for-financial-planning-guidance/)) |
| Cash advance невозможен в РФ (лицензия МФО) | Savings-focused: subscription hunter, AI savings plan | Blue Ocean: Eliminate lending, Create personality |

### Стратегический Выбор: Feature-led Differentiation

Не конкурировать по:
- Функционалу (Дзен-мани: 300+ банков sync)
- Цене (Т-Банк Олег: бесплатно)
- Масштабу (Сбер: 50M users)

Конкурировать по:
- **Experience:** Roast Mode = entertainment layer поверх utility
- **Community:** Challenges, UGC, leaderboards
- **Distribution:** Telegram-native (88M+ MAU в РФ)

---

## 2. Технические Решения (TRIZ)

### Resolved Contradictions

| # | Противоречие | Тип | TRIZ Principle | Решение |
|---|-------------|-----|----------------|---------|
| 1 | Глубокая банковская интеграция vs Open API недоступен | Technical | #13 Other Way Round | Screenshot OCR + CSV. User приносит данные сам |
| 2 | Серьёзный финансовый инструмент vs Развлекательный бот | Physical | Separation in Condition | 3 режима: Roast (fun) / Hype (motivation) / Serious (utility). User переключает |
| 3 | Высокая цена для unit economics vs Низкая для РФ рынка | Physical | Separation in Time | Freemium → pro after proof-of-value. 399₽ = 4.6% от экономии 8,700₽ |
| 4 | Большая команда для скорости vs Маленькая для runway | Physical | Separation in Space | Core team 3 чел + AI automation (LLM делает работу 10 человек) |
| 5 | Низкий CAC для economics vs Quality users для LTV | Technical | #23 Feedback | Viral roast → organic users = low CAC + high-intent (пришли за fun → остаются за utility) |

### Архитектурные Решения

| Решение | Выбор | Альтернативы | Обоснование |
|---------|-------|-------------|-------------|
| Platform | Telegram Bot API | Mobile app, Web app, VK Mini App | 88M+ users, lowest CAC, fastest MVP — [ADR-001](adr/ADR-001-platform-choice.md) |
| Data Input | Screenshot OCR + CSV | Open Banking, Manual only, Scraping | No API until 2027, OCR = good enough — [ADR-005](adr/ADR-005-data-input-method.md) |
| AI Personality | "Дерзкий друг" + tone switching | Single tone, No personality, Formal | A/B testable, wide appeal — [ADR-004](adr/ADR-004-ai-personality.md) |
| LLM Provider | Multi-provider (GPT-4o → Claude → YandexGPT) | Single provider, Self-hosted | Resilience + best quality — [ADR-006](adr/ADR-006-llm-provider.md) |
| Monetization | Freemium (Free/399₽/799₽) | Subscription-only, Ads, Pay-per-use | Proven model, viral free tier — [ADR-003](adr/ADR-003-monetization-model.md) |
| Architecture | Distributed Monolith (Monorepo) | Microservices, Serverless, Monolith | Right-sized for 3-person team + Docker Compose deploy |
| Infrastructure | VPS (AdminVPS/HOSTKEY) + Docker | Cloud (AWS/GCP), Bare metal | Cost-effective for Russia, 152-ФЗ localization |

---

## 3. Competitive Strategy (Game Theory)

### Nash Equilibrium Analysis

```
                    ФинРоуст
                    Roast+Fun         Utility-Only
                  ┌─────────────────┬─────────────────┐
Incumbents        │ (-1, +4)        │ (0, +1)         │
  React quickly   │ We win (unique) │ They match us   │
                  ├─────────────────┼─────────────────┤
Incumbents        │ (+2, +5)        │ (+1, +2)        │
  Ignore us       │ Best case       │ Slow growth     │
                  └─────────────────┴─────────────────┘
```

**Dominant strategy: Roast+Fun.** Wins regardless of competitor reaction.

### Competitive Moat Timeline

```
M0-6:   Brand personality (first-mover в "финансовой прожарке")
M6-12:  Community/UGC (roast-карточки, мемы, challenges)
M12-18: Data moat (транзакционные паттерны Gen Z)
M18+:   AI fine-tuning (модель на русском финансовом юморе)
```

### Counter-strategies

| Threat | Counter |
|--------|---------|
| Т-Банк добавляет юмор в Олега | Углубить community: challenges, leaderboards, UGC. Банк не может создать meme culture |
| Дзен-мани добавляет AI chat | Ускорить iteration. У нас AI-first architecture, у них — legacy |
| Сбер запускает аналог при 100K users | К этому моменту brand + data moat. Сбер = "для взрослых" |
| Новый стартап копирует модель | Personality + community + data = difficult to replicate bundle |

---

## 4. Growth Strategy

### Primary Loop: PLG + Viral Roast

```
Step 1: User видит roast-card друга в чате
    ↓
Step 2: Переходит в Telegram бот → /start → кидает скриншот
    ↓
Step 3: Получает свой roast → 😂 "OMG это правда"
    ↓
Step 4: Шарит roast-card → друзья видят → Step 1 ↻
    ↓
Step 5: Data flywheel: больше users → лучше roasts → больше shares
```

### Channel Mix (по фазам)

| Phase | Primary Channel | CAC | % Budget |
|-------|----------------|:---:|:--------:|
| M1-3 | Viral Telegram + UGC | 50-150₽ | 40% |
| M1-3 | Telegram channel partnerships | 100-300₽ | 40% |
| M2-6 | TikTok/VK Clips | 200-400₽ | 20% |
| M6-12 | Content marketing + SEO | 100-200₽ | 30% |
| M12+ | Paid channels (scale) | 300-500₽ | 40% |

### Tipping Points

| Event | Trigger | Effect |
|-------|---------|--------|
| K-factor > 0.7 | ~3,000 WAR | Organic growth self-sustaining |
| WAR > 5,000 | ~M10-12 | Positive loops dominate negative |
| 50K+ transactions | ~M6 | Data moat becomes significant |
| 100K users | ~M12 | Brand recognition in niche |

---

## 5. Monetization Strategy

### Value Ladder

```
Free (awareness + viral)
  → 1 roast/week, basic categories, top-3 expenses
  → Purpose: demonstrate value, generate viral content

Pro 399₽/мес (core revenue)
  → Unlimited roasts, subscription hunter, challenges, pie-charts
  → Trigger: "Хочу больше прожарок" after 2-3 free roasts

Premium 799₽/мес (power users)
  → AI-коуч с памятью, голосовые, кредитный скоринг, приоритет
  → Trigger: "Хочу персонального финансового коуча"
```

### Paywall Timing (TRIZ: Separation in Time)
1. **M1-3:** Generous free tier (build user base, prove value)
2. **M3-6:** Introduce Pro with clear value delta
3. **M6-12:** Introduce Premium for engaged users
4. **M12+:** Tighten free tier if conversion data supports it

---

## 6. Risk Mitigation Matrix

| Risk | Impact | Probability | Strategy | Owner |
|------|:------:|:-----------:|----------|-------|
| Offensive roast → PR crisis | High | Medium | Content moderation, roast_level system, human review of templates | ML/AI |
| OCR quality insufficient | High | Medium | Top-5 banks training data, user correction loop, CSV fallback | Dev |
| LLM API blocked (sanctions) | High | Medium | Multi-provider failover: GPT-4o → Claude → YandexGPT → GigaChat | Dev |
| Conversion < 3% | Critical | Medium | A/B test paywall, value demonstration, pricing experiments | Growth |
| Content fatigue | Medium | Medium | Template expansion, user-generated roasts, seasonal content | Content |
| Telegram platform risk | Critical | Low | Web app (Phase 2), mobile app (Phase 3) | Product |
| 152-ФЗ violation | High | Low | Legal review, DPO, data localization | Legal |

---

## 7. Phased Execution Plan

### Phase 1: MVP (M1-M3)
**Goal:** Prove Aha Moment works
- Telegram bot + /start onboarding
- Screenshot OCR (top-5 banks)
- Roast Mode AI generation
- Shareable roast cards
- Free tier only
- **KPI:** 2K users, 60% upload→roast, 20% share rate

### Phase 2: Monetization (M4-M6)
**Goal:** Prove willingness to pay
- Pro subscription (399₽)
- Weekly Roast Report
- Challenges + badges
- Subscription hunter
- **KPI:** 20K users, 1K paying, 400K₽ MRR

### Phase 3: Scale (M7-M12)
**Goal:** Reach break-even
- Premium tier (799₽)
- AI-коуч с памятью
- Leaderboards
- Monthly Grade
- Content marketing at scale
- **KPI:** 100K users, 5K paying, 2.5M₽ MRR

### Phase 4: Expand (M13-M24)
**Goal:** Build moat + prepare Series A
- Open Banking API integration (ЦБ)
- Voice roasts
- Web dashboard
- Mobile app preparation
- B2B exploration
- **KPI:** 300K users, 15K paying, 7M₽ MRR

---

## 8. Success Criteria

### North Star Metric
**WAR (Weekly Active Roasters)** — количество пользователей, получивших хотя бы 1 roast за неделю.

### Supporting Metrics

| Category | Metric | M6 Target | M12 Target |
|----------|--------|:---------:|:----------:|
| Acquisition | Bot starts/week | 800 | 3,000 |
| Activation | Upload → Roast completion | 60% | 75% |
| Engagement | WAR | 2,000 | 5,000 |
| Virality | K-factor | 0.5 | 1.2 |
| Revenue | MRR | 702K₽ | 2,834K₽ |
| Retention | D30 retention | 25% | 35% |
| Satisfaction | NPS | 20+ | 40+ |
