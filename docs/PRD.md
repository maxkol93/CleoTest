# Product Requirements Document — ФинРоуст

**Product:** ФинРоуст (AI Финансовый Коуч)  
**Version:** 1.0  
**Author:** SPARC Pipeline  
**Last Updated:** 2026-04-09  
**Status:** Draft

---

## Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-04-08 | Phase 0 Discovery | Initial research (M1-M5) |
| 1.0 | 2026-04-09 | SPARC Pipeline | PRD from Phase 0 data |

---

## 1. Executive Summary

### 1.1 Purpose

ФинРоуст — AI-финансовый коуч для российской молодёжи (18-30 лет), доставляемый через Telegram-бота. Продукт адаптирует модель Cleo AI (US, $500M valuation, 6M+ users) для российского рынка с ключевыми отличиями: Roast Mode на русском языке, Telegram-first доставка, Screenshot OCR вместо Open Banking API.

### 1.2 Scope

**In Scope:**
- Telegram-бот с AI-персонажем ("дерзкий друг")
- Roast Mode / Hype Mode / Serious Mode (переключаемые тоны)
- Загрузка банковских выписок: Screenshot OCR + CSV
- Автоматическая категоризация расходов
- Персонализированные roast-карточки (shareable)
- Freemium модель: Free / Pro (399₽/мес) / Premium (799₽/мес)
- Gamification: challenges, badges, streaks, leaderboards
- Weekly Roast Report, Monthly Financial Grade
- Подписки-детектор (subscription hunter)
- AI-план экономии с tracking

**Out of Scope (Phase 1):**
- Cash Advance / кредитование (требует лицензию МФО) — см. [ADR-003](adr/ADR-003-monetization-model.md)
- Credit Building / скоринг (Credit Builder — Phase 2+)
- Native mobile app (iOS/Android) — Phase 2+
- Open Banking API интеграция (ЦБ Open API — 2027+) — см. [ADR-005](adr/ADR-005-data-input-method.md)
- B2B white-label для банков
- Affiliate рекомендации финансовых продуктов
- Голосовые разговоры (Phase 2+)

### 1.3 Definitions & Acronyms

| Term | Definition |
|------|------------|
| PFM | Personal Finance Management — управление личными финансами |
| Roast Mode | Режим саркастичного анализа расходов с юмором |
| Hype Mode | Мотивационный режим поздравлений за достижения |
| WAR | Weekly Active Roasters — North Star метрика |
| K-factor | Вирусный коэффициент (users invited / user) |
| OCR | Optical Character Recognition — распознавание текста с изображений |
| MCP | Model Context Protocol — протокол интеграции AI-серверов |
| CJM | Customer Journey Map |

---

## 2. Product Vision

### 2.1 Vision Statement

> Стать первым AI-финансовым ассистентом в России, который делает управление деньгами виральным и привычным через юмор и personality-driven UX.

### 2.2 Problem Statement

**Problem:** Российская молодёжь (18-30) не использует budgeting tools — существующие решения (Дзен-мани, CoinKeeper) скучные, utility-only, без personality. 91% используют digital finance, но <5% ведут бюджет.

**Impact:** Gen Z в РФ тратят >70% дохода impulsively, не замечают "подписки-паразиты", не формируют накопления.

**Current Solutions:**
- Дзен-мани (500K users) — функциональный, но скучный
- CoinKeeper (1M users) — простой UI, нет AI
- Т-Банк "Олег" — general assistant, не PFM-focused, только для клиентов Т-Банк
- Сбер GigaChat — general AI, нет financial personality
- Excel / заметки — ручной труд, нет engagement

### 2.3 Strategic Alignment

- Рынок fintech РФ: $3.57B (2024) → $14.66B (2033), CAGR 15.18% — [IMARC](https://www.imarcgroup.com/russia-fintech-market)
- PFM-сегмент: ~$200M SAM в России
- Window of opportunity: 12-18 месяцев до реакции incumbents (Т-Банк, Сбер)
- Стратегия: Feature-led differentiation через personality (Nash Equilibrium analysis — см. [M3](M3_Market_Competition.md))

### 2.4 Success Metrics

| Metric | Current | Target M6 | Target M12 | Timeline |
|--------|---------|-----------|------------|----------|
| Total Users | 0 | 24,400 | 98,400 | 12 мес |
| Paying Users | 0 | 1,464 | 5,904 | 12 мес |
| WAR (Weekly Active Roasters) | 0 | 2,000 | 5,000+ | 12 мес |
| MRR | 0 | 702,720₽ | 2,834K₽ | 12 мес |
| K-factor (viral) | 0 | 0.5 | 1.2 | 12 мес |
| Roast → Share rate | 0 | 20% | 30% | 12 мес |
| Free → Paid conversion | 0 | 6% | 8% | 12 мес |

---

## 3. Target Users

### 3.1 Primary Persona: "Расточитель" (Broke but Self-Aware) — 50%

| Attribute | Description |
|-----------|-------------|
| **Role** | Студент / junior специалист |
| **Demographics** | 18-25 лет, доход 30-60K₽/мес, города 500K+ |
| **Goals** | Дотянуть до зарплаты, не уйти в минус, начать хоть что-то откладывать |
| **Pain Points** | Импульсивные покупки, не видит куда уходят деньги, "budgeting = скука" |
| **Technical Proficiency** | High (digital native, Telegram power user) |
| **Usage Frequency** | Daily (check bot) / Weekly (upload statement) |
| **JTBD Functional** | "Когда снова в минусе за неделю до зарплаты — покажи куда утекли деньги" |
| **JTBD Emotional** | "Хочу чувствовать контроль, а не стыд" |
| **JTBD Social** | "Хочу шарить roast-карточки в чат — это смешно и показывает что я слежу за деньгами" |

### 3.2 Secondary Persona: "Копилка" (Aspiring Saver) — 30%

| Attribute | Description |
|-----------|-------------|
| **Role** | Early career professional |
| **Demographics** | 24-32 года, доход 60-120K₽/мес, Москва/СПб/cities 1M+ |
| **Goals** | Копить на цель (отпуск, техника, первый взнос), отслеживать прогресс |
| **Pain Points** | Пробовал Excel/Дзен-мани — забросил, нет мотивации, скучно |
| **Technical Proficiency** | High |
| **Usage Frequency** | Weekly (goals tracking) / Daily (streak maintenance) |
| **JTBD Functional** | "Когда получаю зарплату — покажи сколько могу отложить без ущерба" |
| **JTBD Emotional** | "Хочу видеть прогресс и чувствовать, что я двигаюсь к цели" |
| **JTBD Social** | "Хочу участвовать в challenges и видеть leaderboard" |

### 3.3 Tertiary Persona: "Финансовый нуб" (Financial Literacy Seeker) — 20%

| Attribute | Description |
|-----------|-------------|
| **Role** | Любой возраст 18-30, хочет разобраться в финансах |
| **Demographics** | Разный доход, часто из регионов |
| **Goals** | Понять куда уходят деньги, научиться считать бюджет |
| **Pain Points** | Финансовая неграмотность, стыд задавать вопросы |
| **Technical Proficiency** | Medium-High |
| **Usage Frequency** | Weekly |

### 3.4 Anti-Personas (Who this is NOT for)
- 35+ "серьёзные" люди — для них Дзен-мани/Excel
- Клиенты с потребностью в кредитовании — нет МФО лицензии
- B2B пользователи — Phase 2+
- Люди без Telegram — наш единственный канал (MVP)

---

## 4. Requirements

### 4.1 Functional Requirements

#### 4.1.1 Feature: Onboarding & Data Input

**Description:** Пользователь подключается через Telegram /start, загружает скриншот банковской выписки или CSV-файл для анализа.

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-001 | Новый user | Начать использовать бот через /start | Получить приветствие и инструкции | Must | S |
| US-002 | User | Загрузить скриншот банковской выписки | Бот распознал мои транзакции через OCR | Must | L |
| US-003 | User | Загрузить CSV-файл выписки | Бот разобрал транзакции из CSV | Must | M |
| US-004 | User | Ввести транзакции вручную | Добавить расходы без скриншота | Should | M |
| US-005 | User | Видеть статус обработки загрузки | Понимать что происходит | Must | S |

**Acceptance Criteria:**

```gherkin
Feature: Onboarding & Data Input

  Scenario: New user starts bot
    Given пользователь открыл Telegram бот "ФинРоуст"
    When отправляет команду /start
    Then получает дерзкое приветствие с personality
    And видит инструкцию по загрузке выписки
    And получает кнопки: "Загрузить скриншот" | "Загрузить CSV" | "Ввести вручную"

  Scenario: Screenshot upload and OCR
    Given пользователь авторизован в боте
    When загружает фото банковской выписки (jpg/png)
    Then система распознаёт текст через OCR
    And извлекает транзакции (дата, сумма, описание)
    And категоризирует расходы с accuracy >= 80%
    And показывает результат для подтверждения

  Scenario: CSV upload
    Given пользователь авторизован в боте
    When загружает CSV-файл (Сбер/Т-Банк/Альфа формат)
    Then система парсит транзакции
    And категоризирует расходы
    And показывает сводку для подтверждения

  Scenario: Invalid input handling
    Given пользователь загружает нечитаемое фото
    When OCR confidence < 50%
    Then бот сообщает: "Не могу разобрать. Попробуй фото получше или CSV"
    And предлагает альтернативные варианты ввода
```

#### 4.1.2 Feature: Roast Mode (Core)

**Description:** AI генерирует саркастичный, но конструктивный анализ расходов пользователя. Главный Aha Moment и viral mechanism.

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-006 | User | Получить roast моих расходов | Увидел свои траты через юмор и задумался | Must | L |
| US-007 | User | Регулировать "остроту" roast (1-5) | Получать roast своего comfort level | Should | M |
| US-008 | User | Переключиться на Hype Mode | Получать мотивацию вместо roast | Must | M |
| US-009 | User | Переключиться на Serious Mode | Получить серьёзный финансовый анализ | Should | M |

**Acceptance Criteria:**

```gherkin
Feature: Roast Mode

  Scenario: Generate roast for user's spending
    Given пользователь загрузил транзакции за период
    When запрашивает /roast
    Then AI генерирует персонализированный roast
    And roast содержит: топ-3 категории перерасхода
    And roast использует русский мем-юмор
    And roast заканчивается конструктивным советом
    And время генерации < 5 секунд

  Scenario: Roast level adjustment
    Given пользователь хочет изменить остроту
    When устанавливает roast_level = 3 (из 5)
    Then последующие roasts соответствуют уровню:
      | Level | Стиль |
      | 1 | Мягкий юмор, подбадривание |
      | 3 | Саркастичный, но добродушный |
      | 5 | Жёсткая прожарка без фильтров |

  Scenario: Tone switching
    Given пользователь в Roast Mode
    When отправляет /hype
    Then тон переключается на мотивационный
    And следующий анализ содержит поздравления и позитив
```

#### 4.1.3 Feature: Roast Card (Shareable)

**Description:** Генерация визуальной карточки с roast для sharing в соцсетях и Telegram-чатах. Основной viral mechanism.

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-010 | User | Получить roast в виде красивой карточки (image) | Поделиться в чате/соцсетях | Must | M |
| US-011 | User | Скрыть точные суммы на карточке | Не палить свои реальные расходы | Should | S |
| US-012 | User | Поделиться карточкой одним тапом | Быстро отправить в чат | Must | S |

**Acceptance Criteria:**

```gherkin
Feature: Roast Card

  Scenario: Generate shareable roast card
    Given пользователь получил roast
    When нажимает "Поделиться"
    Then генерируется изображение 1080x1080
    And карточка содержит: roast-текст, категории расходов, watermark бота
    And суммы можно скрыть (toggle)
    And карточка содержит QR-код / ссылку на бот

  Scenario: Share flow
    Given карточка сгенерирована
    When пользователь нажимает "Отправить в чат"
    Then открывается Telegram share dialog с карточкой
    And добавляется текст "Меня только что прожарил @FinRoastBot 🔥"
```

#### 4.1.4 Feature: Expense Categorization & Analytics

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-013 | User | Видеть расходы по категориям | Понял куда уходят деньги | Must | M |
| US-014 | User | Видеть тренды за неделю/месяц | Отслеживать динамику | Must | M |
| US-015 | User | Получить pie-chart расходов | Визуально увидеть распределение | Should (Pro) | M |
| US-016 | User | Исправить неправильную категорию | AI учился на моих коррекциях | Should | S |

#### 4.1.5 Feature: Subscription Hunter

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-017 | Pro user | Автоматически найти recurring payments | Увидеть скрытые подписки | Must (Pro) | M |
| US-018 | Pro user | Получить alert о новой подписке | Не пропустить нежелательную подписку | Should (Pro) | M |

#### 4.1.6 Feature: Gamification

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-019 | User | Участвовать в Weekly Challenge | Мотивация экономить через игру | Must | M |
| US-020 | User | Видеть мой streak | Мотивация возвращаться каждый день | Should | S |
| US-021 | User | Получить Monthly Grade | Оценить свой прогресс за месяц | Should | M |
| US-022 | User | Видеть badges/достижения | Коллекционировать и шарить | Could | M |

#### 4.1.7 Feature: AI Savings Plan (Pro)

**User Stories:**

| ID | As a... | I want to... | So that... | Priority | Effort |
|----|---------|--------------|------------|----------|--------|
| US-023 | Pro user | Получить AI-рекомендации по экономии | Конкретные советы на основе моих данных | Must (Pro) | L |
| US-024 | Pro user | Установить цель накопления | Отслеживать прогресс к цели | Should (Pro) | M |
| US-025 | Pro user | Получить Weekly Roast Report | Саркастичная сводка за неделю по понедельникам | Must (Pro) | M |

### 4.2 Non-Functional Requirements

#### 4.2.1 Performance

| Metric | Requirement | Rationale |
|--------|-------------|-----------|
| Telegram message response (p50) | < 2s | UX: мгновенный ответ в чате |
| Telegram message response (p99) | < 5s | LLM generation latency |
| Roast generation (p50) | < 4s | AI inference + formatting |
| OCR processing | < 10s | Photo → structured data |
| Concurrent users | 1,000 | M12 target: ~100K users, 10% concurrent |
| Roast card generation | < 3s | Image generation |

#### 4.2.2 Availability & Reliability

| Metric | Requirement |
|--------|-------------|
| Uptime SLA | 99.5% (MVP), 99.9% (M12) |
| RTO | 1 hour |
| RPO | 15 minutes |
| MTTR | 30 minutes |

#### 4.2.3 Security

| Requirement | Implementation |
|-------------|----------------|
| Authentication | Telegram user_id (bot-level auth) |
| Data Encryption (at rest) | AES-256 (PostgreSQL + disk encryption) |
| Data Encryption (in transit) | TLS 1.3 (all connections) |
| PII handling | 152-ФЗ compliance: consent, localization in RF |
| Data retention | User can delete all data via /delete command |
| LLM data | No PII sent to external LLM APIs — только категоризированные суммы |

#### 4.2.4 Scalability

| Dimension | M1 | M6 | M12 | M24 |
|-----------|-----|-----|------|------|
| Users | 2,000 | 24,400 | 98,400 | 320,000 |
| Transactions/day | 5,000 | 60,000 | 250,000 | 800,000 |
| Storage (DB) | 1 GB | 20 GB | 100 GB | 500 GB |
| LLM API calls/day | 1,000 | 12,000 | 50,000 | 160,000 |

### 4.3 Technical Requirements

#### 4.3.1 Platform Support

| Platform | Minimum Version | Notes |
|----------|----------------|-------|
| Telegram Bot API | 7.0+ | Primary delivery channel |
| Telegram clients | iOS 15+ / Android 8+ / Desktop | End-user access |
| Web dashboard | Phase 2 | Admin + analytics |
| Mobile app | Phase 3 | Native iOS/Android |

#### 4.3.2 Integration Requirements

| System | Integration Type | Data Flow | Priority |
|--------|-----------------|-----------|----------|
| Telegram Bot API | Webhook/Long Polling | Bidirectional | Must |
| LLM Provider (GPT-4o primary) | REST API | Out → In | Must |
| LLM Fallback (Claude) | REST API | Out → In | Should |
| LLM Fallback (YandexGPT) | REST API | Out → In | Could |
| OCR Service (Tesseract/Cloud) | REST API / Library | In → Process | Must |
| Payment (YooKassa/CloudPayments) | REST API | Bidirectional | Must |
| Object Storage (S3-compatible) | S3 API | Upload/Download | Must |

#### 4.3.3 Constraints

| Constraint Type | Description | Impact |
|-----------------|-------------|--------|
| Technical | No Open Banking API until 2027 | Screenshot OCR + CSV only — [ADR-005](adr/ADR-005-data-input-method.md) |
| Technical | LLM API access from Russia via proxy | Multi-provider with failover — [ADR-006](adr/ADR-006-llm-provider.md) |
| Business | No МФО license | Cannot offer cash advance or lending |
| Regulatory | 152-ФЗ Personal Data | Servers in Russia, consent required |
| Regulatory | 38-ФЗ Advertising | Roasts must not be financial advice without disclaimer |
| Infrastructure | VPS (AdminVPS/HOSTKEY) | Docker Compose deploy, no Kubernetes |
| Architecture | Distributed Monolith (Monorepo) | All services in one repo, Docker Compose |

---

## 5. User Journeys

### 5.1 Journey: First Roast (Aha Moment)

**Persona:** "Расточитель" (18-25)  
**Goal:** Получить первый roast и захотеть вернуться  
**Trigger:** Увидел roast-карточку друга в Telegram чате

```
┌─────────────────────────────────────────────────────────────┐
│  Step 1: Discovery                                          │
│  ─────────────────                                          │
│  User Action: Видит roast-card в чате → тапает ссылку       │
│  System Response: Открывается @FinRoastBot в Telegram       │
│  Next: Step 2                                               │
├─────────────────────────────────────────────────────────────┤
│  Step 2: Onboarding                                         │
│  ─────────────────                                          │
│  User Action: Нажимает /start                               │
│  System Response: "О, новенький? Давай посмотрим на что ты  │
│  тратишь деньги. Кидай скриншот выписки — я буду             │
│  деликатен. Шучу 🔥"                                        │
│  Next: Step 3                                               │
├─────────────────────────────────────────────────────────────┤
│  Step 3: Data Upload                                        │
│  ─────────────────                                          │
│  User Action: Загружает скриншот выписки из Сбер Online      │
│  System Response: "Обрабатываю... 🔍 Ой-ой, тут есть на что │
│  посмотреть..."                                             │
│  Next: Step 4                                               │
├─────────────────────────────────────────────────────────────┤
│  Step 4: 🔥 AHA MOMENT — First Roast                       │
│  ────────────────────────────────────                       │
│  User Action: Ждёт результат                                │
│  System Response: Персонализированный roast расходов:       │
│  "Яндекс.Еда 12 раз за неделю? Ты что, разучился          │
│  включать плиту? На доставку ушло 8,400₽ — это абонемент   │
│  в спортзал на 2 месяца. Хотя, судя по тратам, спортзал    │
│  тебе не грозит 💀"                                         │
│  + Кнопка [Поделиться 🔥] [Ещё прожарь!] [Хватит 😅]       │
│  Outcome: Пользователь смеётся, показывает друзьям          │
└─────────────────────────────────────────────────────────────┘
```

**Error Paths:**

| Step | Error Condition | System Response | User Recovery |
|------|-----------------|-----------------|---------------|
| 3 | OCR не может распознать | "Фото размытое. Попробуй ещё раз или кинь CSV" | Повторная загрузка / CSV |
| 3 | Unsupported bank format | "Этот банк пока не поддерживаю. Попробуй CSV" | CSV upload |
| 4 | LLM timeout | "Мне нужно подумать подольше... ⏳" → retry | Auto-retry через 5s |
| 4 | LLM all providers down | "Временные технические работы. Попробуй через 5 мин" | Retry later |

### 5.2 Journey: Weekly Engagement Loop

**Persona:** "Копилка" (24-32)  
**Goal:** Отслеживать прогресс к цели накопления  
**Trigger:** Push notification в понедельник (Weekly Roast Report)

```
┌─────────────────────────────────────────────────────────────┐
│  Step 1: Weekly Report (Monday 10:00)                       │
│  User receives: "Еженедельная прожарка готова! 🔥"          │
│  → Opens bot                                                │
├─────────────────────────────────────────────────────────────┤
│  Step 2: View Report                                        │
│  Bot shows: саркастичный summary расходов за неделю         │
│  + прогресс к цели + Challenge of the Week                  │
├─────────────────────────────────────────────────────────────┤
│  Step 3: Accept Challenge                                   │
│  User: принимает challenge "Потрать на еду < 5,000₽"       │
│  Bot: "Вызов принят! Посмотрим, хватит ли силы воли 💪"    │
├─────────────────────────────────────────────────────────────┤
│  Step 4: Mid-week Check                                     │
│  Bot (среда): "Уже 3,200₽ на еду. Осталось 1,800₽ на      │
│  3 дня. Удачи, макаронный воин 🍝"                          │
├─────────────────────────────────────────────────────────────┤
│  Step 5: Challenge Result (Sunday)                          │
│  Bot: "Challenge выполнен! 4,800₽ вместо обычных 7,200₽.   │
│  Сэкономлено: 2,400₽. Badge: Повелитель Макарон 🏆"        │
└─────────────────────────────────────────────────────────────┘
```

---

## 6. UI/UX Requirements

### 6.1 Design Principles

- **Telegram-native:** Все взаимодействия через Telegram UI (inline buttons, keyboards, cards)
- **Personality-first:** Каждое сообщение бота имеет personality (дерзкий, но добрый)
- **Mobile-first:** Roast-карточки 1080x1080, оптимизированы для мобильного sharing
- **Quick actions:** Inline keyboards для частых действий (1-2 тапа до результата)
- **Progressive disclosure:** Free показывает ценность, Pro раскрывает полный функционал

### 6.2 Key Screens/Views

| Screen | Purpose | Key Elements |
|--------|---------|--------------|
| /start | Onboarding | Приветствие с personality, инструкция по загрузке |
| Upload flow | Data input | Inline buttons выбора метода, progress indicator |
| Roast result | Core value | Текст roast, кнопки share/more/stop |
| Roast card | Sharing | 1080x1080 image с roast, watermark, QR |
| Dashboard | Analytics | Категории расходов, тренды, прогресс |
| Settings | Configuration | Roast level, tone mode, notifications |
| Subscription | Monetization | Comparison Free/Pro/Premium, payment flow |

### 6.3 Accessibility Requirements
- Текстовые альтернативы для всех изображений (alt-text в сообщениях)
- Поддержка screen readers через Telegram accessibility features
- Контрастные roast-карточки (WCAG 2.1 AA color contrast)

---

## 7. Release Strategy

### 7.1 MVP (Phase 1) — M1-M3

**Timeline:** M1-M3

**Features:**
| Feature | Priority | Status |
|---------|----------|--------|
| Telegram bot /start + onboarding | Must | Planned |
| Screenshot OCR (Сбер, Т-Банк) | Must | Planned |
| CSV upload (universal format) | Must | Planned |
| Roast Mode (AI generation) | Must | Planned |
| Roast card (shareable image) | Must | Planned |
| Basic expense categorization | Must | Planned |
| Free tier (1 roast/week) | Must | Planned |

**Success Criteria:**
- [ ] 2,000 bot starts
- [ ] 60% upload → roast completion
- [ ] 20% roast → share rate
- [ ] NPS > 20

### 7.2 v1.0 (Phase 2) — M4-M6

**Features:**
| Feature | Priority | Status |
|---------|----------|--------|
| Pro subscription (399₽/мес) | Must | Planned |
| Unlimited roasts | Must | Planned |
| Subscription Hunter | Should | Planned |
| Weekly Roast Report | Should | Planned |
| Challenges + Badges | Should | Planned |
| Streak System | Should | Planned |
| Pie-charts (Telegram inline) | Could | Planned |

### 7.3 Future Phases

| Phase | Features | Tentative Timeline |
|-------|----------|-------------------|
| v1.1 (M7-9) | Premium tier, AI-коуч с памятью, Manual input improvements | M7-M9 |
| v1.2 (M10-12) | Push notification parsing, Monthly Grade, Leaderboards | M10-M12 |
| v2.0 (M13-18) | Open Banking API (ЦБ), голосовые, web dashboard | M13-M18 |
| v3.0 (M19-24) | Mobile app (iOS/Android), B2B white-label, affiliate | M19-M24 |

---

## 8. Dependencies

### 8.1 Internal Dependencies

| Dependency | Owner | Impact | Status |
|------------|-------|--------|--------|
| AI Personality Engine (system prompts) | ML/AI Engineer | Core — roast quality | Planned |
| OCR Pipeline | Fullstack Dev | Core — data input | Planned |
| Payment Integration | Fullstack Dev | Monetization | Planned (M3+) |

### 8.2 External Dependencies

| Dependency | Provider | Risk Level | Mitigation |
|------------|----------|------------|------------|
| Telegram Bot API | Telegram | Low | Stable, widely used |
| LLM API (GPT-4o) | OpenAI | Medium | Multi-provider fallback — [ADR-006](adr/ADR-006-llm-provider.md) |
| LLM Fallback (Claude) | Anthropic | Medium | Secondary provider |
| LLM Fallback (YandexGPT) | Yandex | Low | Russian provider, no sanctions risk |
| VPS Hosting | AdminVPS/HOSTKEY | Low | Standard Russian VPS providers |
| Payment Gateway | YooKassa/CloudPayments | Low | Standard Russian payment |
| OCR | Tesseract / Yandex Vision | Low | Self-hosted fallback available |

---

## 9. Risks & Mitigations

| Risk ID | Description | Probability | Impact | Mitigation Strategy |
|---------|-------------|-------------|--------|---------------------|
| R-001 | Roast Mode offensive → bad PR | Medium | High | Moderation layer, roast_level system, content guidelines |
| R-002 | OCR accuracy low for Russian bank statements | Medium | High | Train on top-5 banks, manual correction UX |
| R-003 | LLM API blocked from Russia (sanctions) | Medium | High | Multi-provider + Russian fallbacks (YandexGPT, GigaChat) |
| R-004 | Low Free→Paid conversion (<3%) | Medium | Critical | A/B test paywall timing, value demonstration |
| R-005 | Telegram bot blocking by platform | Low | Critical | Diversify to web app in Phase 2 |
| R-006 | 152-ФЗ compliance issues | Low | High | Legal review, data localization, consent flows |
| R-007 | Content fatigue (same roast patterns) | Medium | Medium | Expanding roast template library, user feedback loop |
| R-008 | Competitor reaction (Т-Банк adds gamification) | Medium | Medium | Deepen community moat, UGC, challenges — [M3 Game Theory](M3_Market_Competition.md) |

---

## 10. Open Questions

| ID | Question | Owner | Due Date | Resolution |
|----|----------|-------|----------|------------|
| Q-001 | Optimal roast_level default for Russian audience? | Growth | M1 beta | A/B test |
| Q-002 | Which top-5 Russian banks to support OCR first? | Product | M1 | Research bank market share |
| Q-003 | YooKassa vs CloudPayments for Telegram payments? | Dev | M2 | Technical evaluation |
| Q-004 | 152-ФЗ: need DPO (Data Protection Officer)? | Legal | M1 | Legal consultation |
| Q-005 | Telegram Payments API vs external payment page? | Dev | M2 | UX + fee comparison |

---

## 11. Appendices

### A. Research Findings
See [Research_Findings.md](Research_Findings.md) and [Micro_Trends_Research.md](Micro_Trends_Research.md)

### B. Competitive Analysis
See [M3_Market_Competition.md](M3_Market_Competition.md) — full competitive matrix, game theory, Blue Ocean analysis

### C. Technical Specifications
See [Architecture.md](Architecture.md), [Pseudocode.md](Pseudocode.md), [ADR files](adr/)

### D. Business Model
See [M4_Business_Finance.md](M4_Business_Finance.md) — unit economics, P&L, funding roadmap

### E. Growth Engine
See [M5_Growth_Engine.md](M5_Growth_Engine.md) — viral loop, channels, retention, moats

---

*Document generated with SPARC PRD Generator skill*
