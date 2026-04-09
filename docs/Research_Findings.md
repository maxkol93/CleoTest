# Research Findings — ФинРоуст
**Режим:** DEEP (GOAP A* + OODA) | **Дата:** 2026-04-09

---

## 1. Исходный Продукт: Cleo AI

### Ключевые факты (Confidence: 0.89)

| Параметр | Значение | Источник |
|----------|----------|----------|
| Год основания | 2016, Лондон | [Crunchbase](https://www.crunchbase.com/organization/cleo-ai) |
| Пользователи | 6M+ total, 850K+ paying | [Sacra](https://sacra.com/c/cleo/) |
| Revenue | $136M (2024), $280M+ ARR (2025) | [meetcleo.com](https://web.meetcleo.com/2024-annual-report), [ARR Club](https://www.arr.club/signal/cleo-arr-hits-250m) |
| Valuation | $500M (Series C, Jun 2022) | [Crunchbase](https://www.crunchbase.com/organization/cleo-ai) |
| Funding | ~$142-175M total | [Crunchbase](https://www.crunchbase.com/organization/cleo-ai) |
| Tech stack | OpenAI o3, Python ML, Node.js, Plaid | [BusinessWire](https://www.businesswire.com/news/home/20250729690058/en/) |
| Key differentiator | Roast Mode — personality-driven financial coaching | [meetcleo.com/blog](https://web.meetcleo.com/blog/the-money-app-that-roasts-you) |
| FTC settlement | $17M (Mar 2025) за misleading advertising | [FTC](https://www.ftc.gov/news-events/news/press-releases/2025/03/) |

### Cleo 3.0 (Jul 2025)
- Голосовые разговоры (TTS + conversational AI)
- Long-term memory (запоминает цели, привычки)
- Advanced reasoning на OpenAI o3
- **Значение для нас:** устанавливает ожидания рынка от AI financial assistant

### Уроки из Cleo (что копировать vs что адаптировать)

| Аспект | Копировать | Адаптировать | Исключить |
|--------|------------|--------------|-----------|
| AI Personality (Roast/Hype) | Концепцию тонов | Русский юмор, мемы, сленг | English-specific jokes |
| Gamification | Badges, streaks, challenges | Локализация challenges под РФ траты | — |
| Cash Advance | — | — | Требует лицензию МФО |
| Credit Builder | — | — | Не применимо в РФ (кредитные бюро) |
| Plaid integration | Концепцию bank sync | Screenshot OCR + CSV (нет Open Banking до 2027) | Plaid API |
| Freemium model | Tiered pricing | Цены для РФ рынка (399/799₽) | US pricing ($5.99-14.99) |

**Подробнее:** [M1_Fact_Sheet.md](M1_Fact_Sheet.md), [M2_Product_Customers.md](M2_Product_Customers.md)

---

## 2. Целевой Рынок: Россия

### Размер рынка

| Уровень | Размер | Confidence | Источник |
|---------|--------|:----------:|----------|
| TAM (Global PFM) | $1.94B к 2032 | 0.85 | [DataBridge](https://www.databridgemarketresearch.com/reports/global-personal-finance-management-market) |
| SAM (Russia PFM) | ~$200M | 0.75 | [IMARC](https://www.imarcgroup.com/russia-fintech-market) |
| SOM (AI PFM 18-30, 3 года) | $15-25M | 0.65 | Расчёт top-down + bottom-up |

**Bottom-up проверка:** 5.5-6M потенциальных users × 5-8% конверсия × 4,800₽/год = $15-26M → **<5% расхождение с top-down**

### Демография целевой аудитории

| Параметр | Значение | Источник |
|----------|----------|----------|
| Население 18-30 лет | ~20-22M | [Worldometers](https://www.worldometers.info/demographics/russia-demographics/) |
| Используют digital finance | 91% молодёжи | [ЦБ РФ](https://cbr.ru/analytics/szpp/fin_literacy/research/fin_ed_5/) |
| Открыты к AI для финансов | 62% Gen Z | [PYMNTS](https://www.pymnts.com/credit-unions/2025/62percent-of-gen-z-turn-to-ai-for-financial-planning-guidance/) |
| Telegram MAU в РФ | 88M+ | [Mediascope](https://mediascope.net/) |
| Fintech рост РФ | $3.57B→$14.66B, CAGR 15.18% | [IMARC](https://www.imarcgroup.com/russia-fintech-market) |

**Подробнее:** [M3_Market_Competition.md](M3_Market_Competition.md)

---

## 3. Микро-тренды (10 исследованных)

| # | Тренд | Impact | Timeframe | Confidence | Action |
|---|-------|:------:|-----------|:----------:|--------|
| 1 | AI Hyper-Personalization | High | Now | 0.90 | Core feature: персонализированные roasts |
| 2 | Gamification in Finance | High | Now | 0.85 | Challenges, badges, streaks, leaderboards |
| 3 | Gen Z Trust in AI Finance | High | Now | 0.85 | Marketing: "AI-друг, не банковский бот" |
| 4 | Open Banking Russia (2027) | Medium | 2027+ | 0.90 | Phase 2: bank sync через ЦБ Open API |
| 5 | Financial Literacy Paradox RU | High | Now | 0.80 | Edutainment: финграмотность через roast |
| 6 | Russian PFM Market Gap | High | Now | 0.85 | Blue Ocean: personality-driven PFM |
| 7 | Embedded Finance | Medium | 2025-2027 | 0.80 | Phase 3: affiliate рекомендации |
| 8 | Voice-First Finance | Medium | 2026+ | 0.85 | Phase 2: голосовые roasts |
| 9 | Digital Ruble | Low-Med | 2026-2028 | 0.75 | Monitor: новые возможности для fintech |
| 10 | Russian Banks AI Investment | Medium | Now | 0.80 | Competitive awareness: Т-Банк, Сбер активны |

**Подробнее:** [Micro_Trends_Research.md](Micro_Trends_Research.md)

---

## 4. Конкурентный Ландшафт

### Матрица позиционирования

| Конкурент | AI Personality | Telegram | Gamification | Наш gap |
|-----------|:-:|:-:|:-:|---------|
| Дзен-мани | No | No | No | **Всё** — utility only |
| CoinKeeper | No | No | Basic | Personality + Telegram |
| Т-Банк Олег | General | Yes | No | PFM focus + gamification + fun |
| Сбер GigaChat | General | No | No | PFM focus + Telegram + personality |

### Blue Ocean: 4 Actions

| Действие | Что | TRIZ Principle |
|----------|-----|----------------|
| Eliminate | Cash advance, credit building | #2 Extraction |
| Reduce | Bank sync (screenshot only) | #1 Segmentation |
| Raise | AI personality + gamification | #3 Local Quality |
| **Create** | **Roast на русском + Telegram-native** | #13 Other Way Round |

### Game Theory: Nash Equilibrium

**Оптимальная стратегия:** Roast+Fun positioning.
- Incumbents не могут добавить "дерзкий personality" в банковское приложение (reputation risk)
- Window of opportunity: 12-18 месяцев
- Counter при реакции Т-Банк: углубить community (challenges, leaderboards, UGC)

**Подробнее:** [M3_Market_Competition.md](M3_Market_Competition.md)

---

## 5. Бизнес-модель

### Unit Economics

| Метрика | Значение | Confidence |
|---------|----------|:----------:|
| ARPU (blended) | 480₽/мес ($5.5) | 0.85 |
| CAC | 250₽ ($2.9) | 0.70 |
| LTV | 5,486₽ ($63) | 0.65 |
| LTV:CAC | 22:1 | 0.65 |
| Payback | ~20 дней | 0.65 |
| Gross Margin | 80% | 0.80 |
| Monthly Churn | 7% | 0.70 |

### Pricing

| Tier | Цена | Target |
|------|------|--------|
| Free | 0₽ | 1 roast/неделю, awareness + viral |
| Pro | 399₽/мес (3,499₽/год) | Безлимит roasts, challenges, analytics |
| Premium | 799₽/мес (6,999₽/год) | AI-коуч с памятью, голосовые, приоритет |

### Ключевой риск
**Конверсия Free→Paid.** При <3% модель не работает. При 6% (target) — break-even M12.

**Подробнее:** [M4_Business_Finance.md](M4_Business_Finance.md)

---

## 6. Growth Engine

### Primary Loop: PLG + Viral Roast

```
User видит roast-card → переходит в бот → получает свой roast → шарит → friends видят → loop
```

### Каналы

| Канал | CAC | Scale | Timing |
|-------|:---:|:-----:|--------|
| Viral Telegram + UGC | 50-150₽ | High | M1+ |
| TikTok/VK Clips | 200-400₽ | High | M2-4 |
| Telegram channel partnerships | 100-300₽ | Medium | M1-3 |

### 5 Moats (ранжированы)

1. **Brand personality** — первый AI-финансовый roaster в РФ
2. **Data moat** — транзакционные паттерны Gen Z
3. **Community/UGC** — roast-карточки, мемы, challenges
4. **Distribution (Telegram)** — first-mover в Telegram PFM
5. **AI fine-tuning** — модель на русском финансовом юморе

**Подробнее:** [M5_Growth_Engine.md](M5_Growth_Engine.md)

---

## 7. Регуляторная среда

| Регуляция | Статус | Риск | Действие |
|-----------|--------|:----:|----------|
| 152-ФЗ (Персональные данные) | Действует | Medium | Серверы в РФ, согласие, DPO |
| Open Banking API (ЦБ) | 2027 | Low | Phase 2 — пока screenshot/CSV |
| Лицензия МФО | Не требуется для PFM | Low | Не добавлять кредитование |
| 38-ФЗ (Реклама) | Действует | Medium | Disclaimer в roasts |
| 135-ФЗ (Антимонопольное) | Действует | Low | Мониторинг |

**Ключевой вывод:** PFM без кредитования = minimal regulatory burden.

---

## 8. Архитектурные Решения (ADR)

| ADR | Решение | Ключевой аргумент |
|-----|---------|-------------------|
| [ADR-001](adr/ADR-001-platform-choice.md) | Telegram-first | 88M+ RU users, CAC 3-5x ниже |
| [ADR-002](adr/ADR-002-aha-moment.md) | Roast Mode как Aha Moment | Virality > conversion rate |
| [ADR-003](adr/ADR-003-monetization-model.md) | Freemium с proof-of-value | 399₽ = 4.6% от экономии 8,700₽ |
| [ADR-004](adr/ADR-004-ai-personality.md) | "Дерзкий друг" persona | Tone switching: Roast/Hype/Serious |
| [ADR-005](adr/ADR-005-data-input-method.md) | Screenshot OCR + CSV | No Open Banking until 2027 |
| [ADR-006](adr/ADR-006-llm-provider.md) | Multi-LLM (GPT-4o primary) | Failover chain: GPT-4o → Claude → YandexGPT → GigaChat |

---

## 9. CJM: Выбранный Вариант

**Гибрид A+C:** "ФинРоуст в Telegram" (score: 25/30 vs A=21, B=20, C=18)

6 стадий: Awareness → Activation → Engagement → Monetization → Retention → Advocacy

**North Star Metric:** Weekly Active Roasters (WAR)

**Подробнее:** [CJM_Variants.md](CJM_Variants.md), [CJM_Variants.html](CJM_Variants.html)

---

## Confidence Summary

| Блок исследования | Avg Confidence |
|-------------------|:--------------:|
| Cleo AI (M1) | 0.89 |
| Product & Customers (M2) | 0.87 |
| Market & Competition (M3) | 0.82 |
| Business & Finance (M4) | 0.71 |
| Growth Engine (M5) | 0.68 |
| Micro-Trends | 0.84 |
| **Общий** | **0.80** |

**Самый низкий confidence:** Growth metrics (0.60) — гипотезы для валидации на MVP.
