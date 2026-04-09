# Specification — ФинРоуст
**Version:** 1.0 | **Дата:** 2026-04-09

---

## 1. Functional Requirements

### 1.1 Feature: Telegram Bot Core

#### US-001: Bot Initialization

| Field | Value |
|-------|-------|
| As a | Новый пользователь |
| I want to | Запустить бот командой /start |
| So that | Получить приветствие и понять как пользоваться |
| Priority | Must |
| Effort | S |

```gherkin
Feature: Bot Initialization

  Scenario: First-time user starts the bot
    Given пользователь никогда не взаимодействовал с @FinRoastBot
    When отправляет /start
    Then создаётся запись user в БД с telegram_id и created_at
    And бот отвечает дерзким приветствием (personality = "дерзкий друг")
    And показывает inline keyboard:
      | Button | Callback |
      | Загрузить скриншот | upload_screenshot |
      | Загрузить CSV | upload_csv |
      | Ввести вручную | manual_input |
    And время ответа < 2 секунды

  Scenario: Returning user starts the bot
    Given пользователь уже зарегистрирован в системе
    When отправляет /start
    Then бот приветствует с упоминанием последней активности
    And показывает основное меню с учётом subscription tier
```

#### US-002: Help & Commands Menu

```gherkin
Feature: Bot Commands

  Scenario: User requests help
    Given пользователь авторизован
    When отправляет /help
    Then бот показывает список команд с описанием:
      | Command | Description |
      | /roast | Прожарить мои расходы |
      | /hype | Режим мотивации |
      | /serious | Серьёзный анализ |
      | /stats | Статистика расходов |
      | /challenge | Текущий challenge |
      | /streak | Мой streak |
      | /settings | Настройки |
      | /subscribe | Подписка Pro/Premium |
      | /delete | Удалить все мои данные |
```

### 1.2 Feature: Data Input — Screenshot OCR

#### US-003: Screenshot Upload & Processing

| Field | Value |
|-------|-------|
| As a | Пользователь |
| I want to | Загрузить скриншот банковской выписки |
| So that | Бот распознал мои транзакции автоматически |
| Priority | Must |
| Effort | L |

```gherkin
Feature: Screenshot OCR

  Scenario: Valid screenshot from supported bank
    Given пользователь нажал "Загрузить скриншот"
    When отправляет фото выписки из Сбербанк Online (jpg/png, < 10MB)
    Then бот отвечает "Обрабатываю... 🔍" в течение 1 секунды
    And OCR извлекает текст с confidence >= 70%
    And парсер определяет банк-источник
    And извлекает транзакции: дата, сумма, описание, тип (income/expense)
    And AI категоризирует каждую транзакцию (accuracy >= 80%)
    And показывает результат:
      """
      Нашёл X транзакций за [период]:
      🍔 Еда: X,XXX₽ (XX%)
      🚕 Транспорт: X,XXX₽ (XX%)
      🎮 Развлечения: X,XXX₽ (XX%)
      [Всё верно?] [Исправить]
      """
    And общее время обработки < 10 секунд

  Scenario: Low quality screenshot
    Given пользователь отправляет размытое фото
    When OCR confidence < 50%
    Then бот отвечает: "Не могу разобрать это фото. Попробуй:"
    And показывает советы: "Фото в хорошем освещении / CSV файл / Ручной ввод"

  Scenario: Unsupported bank format
    Given пользователь отправляет скриншот из неподдерживаемого банка
    When парсер не может определить формат
    Then бот отвечает: "Этот банк пока не поддерживаю. Кинь CSV или введи вручную"
    And логирует банк-формат для будущей поддержки

  Scenario: Supported banks (MVP)
    Given система поддерживает OCR для следующих банков:
      | Bank | Format | Priority |
      | Сбербанк Online | Mobile screenshot | Must |
      | Т-Банк (Tinkoff) | Mobile screenshot | Must |
      | Альфа-Банк | Mobile screenshot | Should |
      | ВТБ | Mobile screenshot | Should |
      | Райффайзен | Mobile screenshot | Could |
```

### 1.3 Feature: Data Input — CSV Upload

#### US-004: CSV File Processing

```gherkin
Feature: CSV Upload

  Scenario: Valid CSV from supported bank
    Given пользователь нажал "Загрузить CSV"
    When отправляет CSV файл (< 5MB) из Сбербанк
    Then парсер определяет формат CSV (delimiter, encoding, column mapping)
    And извлекает транзакции: дата, сумма, описание
    And AI категоризирует расходы
    And показывает сводку для подтверждения
    And время обработки < 5 секунд

  Scenario: Universal CSV format
    Given пользователь загружает CSV с колонками: дата, сумма, описание
    When формат не из известного банка
    Then парсер пытается авто-определить колонки
    And если не получается — просит пользователя указать mapping:
      "Какая колонка — дата? Какая — сумма?"

  Scenario: Invalid CSV
    Given пользователь загружает файл
    When файл не является валидным CSV (или > 5MB)
    Then бот: "Это не похоже на CSV. Нужен файл .csv из банковского приложения"
```

### 1.4 Feature: Manual Input

#### US-005: Manual Transaction Entry

```gherkin
Feature: Manual Input

  Scenario: Quick expense entry
    Given пользователь выбрал "Ввести вручную"
    When отправляет текст "кофе 350"
    Then бот парсит: категория="Еда/Кафе", сумма=350₽, дата=сегодня
    And сохраняет транзакцию
    And подтверждает: "Записал: Кафе 350₽ ☕ [Исправить категорию]"

  Scenario: Expense with details
    Given пользователь отправляет "вчера такси 890р"
    When текст содержит дату и сумму
    Then бот парсит: категория="Транспорт", сумма=890₽, дата=вчера
    And сохраняет и подтверждает

  Scenario: Ambiguous input
    Given пользователь отправляет "потратил деньги"
    When не указана сумма
    Then бот: "Сколько потратил? И на что?"
```

### 1.5 Feature: Roast Mode (Core)

#### US-006: Generate Personalized Roast

| Field | Value |
|-------|-------|
| As a | Пользователь с загруженными данными |
| I want to | Получить roast моих расходов |
| So that | Увидеть свои финансовые привычки через юмор |
| Priority | Must |
| Effort | L |

```gherkin
Feature: Roast Mode

  Scenario: Generate roast from uploaded data
    Given пользователь имеет >= 5 транзакций за последний период
    When отправляет /roast
    Then бот генерирует персонализированный roast:
      - Содержит топ-3 категории перерасхода
      - Использует русский мем-юмор и сленг
      - Содержит конкретные суммы (или % бюджета)
      - Заканчивается 1 конструктивным советом
      - Длина: 100-300 символов
    And время генерации < 5 секунд
    And показывает кнопки: [Поделиться 🔥] [Ещё!] [Хватит 😅]

  Scenario: Roast with insufficient data
    Given пользователь имеет < 5 транзакций
    When отправляет /roast
    Then бот: "Маловато данных для прожарки. Кинь ещё выписку — мне нужен материал 🔍"

  Scenario: Free tier roast limit
    Given пользователь на Free tier использовал 1 roast на этой неделе
    When запрашивает ещё один roast
    Then бот: "Лимит прожарок на этой неделе исчерпан. Хочешь безлимит? 👀"
    And показывает кнопку [Подписка Pro — 399₽/мес]

  Scenario: Roast level adjustment
    Given пользователь отправляет /settings
    When выбирает "Острота прожарки" и устанавливает уровень 1-5
    Then последующие roasts соответствуют уровню:
      | Level | Style | Example tone |
      | 1 | Мягкий, дружелюбный | "Может, стоит чуть реже заказывать еду?" |
      | 2 | Лёгкая ирония | "Яндекс.Еда — твой лучший друг, да?" |
      | 3 | Саркастичный (default) | "12 заказов за неделю? Ты что, плиту продал?" |
      | 4 | Жёсткий | "Если твой холодильник умел бы говорить, он бы плакал от одиночества" |
      | 5 | Максимальная прожарка | "Курьер из Delivery Club уже знает код от подъезда. Поздравляю." |
```

#### US-007: Tone Switching

```gherkin
Feature: Tone Switching

  Scenario: Switch to Hype Mode
    Given пользователь в Roast Mode
    When отправляет /hype
    Then тон переключается на мотивационный
    And следующий анализ содержит позитив и поздравления
    And бот: "Режим Мотивации активирован! Давай посмотрим на твои победы 💪"

  Scenario: Switch to Serious Mode
    Given пользователь в любом режиме
    When отправляет /serious
    Then тон переключается на нейтрально-аналитический
    And следующий анализ без юмора, с конкретными цифрами и рекомендациями

  Scenario: Switch back to Roast
    Given пользователь в Hype или Serious режиме
    When отправляет /roast
    Then тон возвращается к саркастичному (текущий roast_level)
```

### 1.6 Feature: Roast Card (Shareable)

#### US-008: Generate & Share Roast Card

```gherkin
Feature: Roast Card

  Scenario: Generate roast card image
    Given пользователь получил roast
    When нажимает [Поделиться 🔥]
    Then генерируется изображение 1080x1080px:
      - Фон: тёмный с gradient
      - Текст: roast-фраза (крупный шрифт)
      - Категории расходов: иконки + проценты (без точных сумм по дефолту)
      - Watermark: @FinRoastBot + QR-код на бот
      - Branding: логотип ФинРоуст
    And время генерации < 3 секунд

  Scenario: Share roast card
    Given карточка сгенерирована
    When пользователь нажимает "Отправить в чат"
    Then открывается Telegram share picker
    And к карточке прикрепляется текст "Меня только что прожарил @FinRoastBot 🔥"

  Scenario: Privacy toggle on card
    Given пользователь генерирует карточку
    When нажимает "Скрыть суммы"
    Then на карточке суммы заменяются на проценты или "***₽"
    And категории остаются видимыми
```

### 1.7 Feature: Expense Analytics

#### US-009: View Expense Categories

```gherkin
Feature: Expense Analytics

  Scenario: View category breakdown
    Given пользователь имеет транзакции за текущий месяц
    When отправляет /stats
    Then бот показывает:
      - Топ-5 категорий с суммами и процентами
      - Сравнение с прошлым месяцем (если есть данные)
      - Emoji иконки для каждой категории
    And для Pro: pie-chart (inline image)

  Scenario: Correct category
    Given бот показал категоризированные расходы
    When пользователь нажимает [Исправить] на конкретной транзакции
    Then показывает список категорий для выбора
    And сохраняет коррекцию как training data
    And подтверждает: "Исправлено! Буду умнее 🧠"
```

### 1.8 Feature: Subscription Hunter (Pro)

#### US-010: Detect Recurring Payments

```gherkin
Feature: Subscription Hunter

  Scenario: Detect recurring subscription
    Given Pro пользователь загрузил >= 2 месяцев данных
    When система находит recurring payment (same amount, same merchant, monthly)
    Then уведомляет: "Нашёл подписку-паразит: [Merchant] [Amount]₽/мес. Ты знал?"
    And показывает кнопки: [Да, нужна] [Забыл про неё 😱] [Отменить]

  Scenario: Subscription summary
    Given Pro пользователь запрашивает /subscriptions
    Then бот показывает список всех обнаруженных recurring payments:
      | Подписка | Сумма | Частота | Статус |
    And показывает total: "Всего на подписки: X,XXX₽/мес"
    And саркастичный комментарий: "X сервисов стримят деньги с твоей карты. Как минимум Y из них ты точно не используешь"
```

### 1.9 Feature: Gamification

#### US-011: Weekly Challenge

```gherkin
Feature: Weekly Challenge

  Scenario: New weekly challenge (Monday)
    Given сегодня понедельник 10:00 MSK
    When система генерирует challenge
    Then бот отправляет: "Challenge недели: [Описание]. Принимаешь? 💪"
    And challenge examples:
      | Challenge | Target | Difficulty |
      | Потрать на еду < 5,000₽ | Сократить расходы на еду | Medium |
      | 3 дня без такси | Отказаться от такси | Easy |
      | Неделя без Wildberries | Не покупать на маркетплейсе | Hard |
    And показывает [Принять] [Пропустить]

  Scenario: Challenge progress mid-week
    Given пользователь принял challenge
    When наступает среда
    Then бот отправляет update: "Прогресс по challenge: X из Y. [Мотивация/Прожарка]"

  Scenario: Challenge completed
    Given пользователь выполнил challenge к воскресенью
    Then бот поздравляет + выдаёт badge
    And badge отображается в профиле

  Scenario: Challenge failed
    Given пользователь НЕ выполнил challenge
    Then бот: "Challenge провален. Но не расстраивайся — на следующей неделе отыграешься 😏"
```

#### US-012: Streak System

```gherkin
Feature: Streak System

  Scenario: Maintain daily streak
    Given пользователь взаимодействовал с ботом вчера
    When взаимодействует сегодня (upload, /roast, /stats, challenge update)
    Then streak counter увеличивается на 1
    And бот показывает: "🔥 Streak: X дней"

  Scenario: Streak milestone
    Given streak достигает 7 / 14 / 30 / 60 / 100 дней
    Then бот отправляет поздравление + badge
    And при 30 днях: "Месяц следишь за деньгами! Ты уже в топ-5% пользователей 🏆"

  Scenario: Streak broken
    Given пользователь не взаимодействовал > 24 часов
    Then streak сбрасывается
    And при следующем визите: "Streak потерян 💀 Начинаем заново. Дней: 1"
```

#### US-013: Monthly Financial Grade

```gherkin
Feature: Monthly Financial Grade

  Scenario: Monthly grade report (1st of month)
    Given наступил 1-й день нового месяца
    When у пользователя есть данные за прошлый месяц
    Then бот генерирует оценку:
      - Grade: F / D / C / B / A / S
      - Criteria: % бюджета сэкономлено, challenges completed, streak days
      - Comparison: с прошлым месяцем
      - Roast/Hype comment based on grade
    And пример: "Твоя оценка за март: C+ (рост с D!). Ещё чуть-чуть и дотянешь до B. Или нет 😏"
```

### 1.10 Feature: Weekly Roast Report (Pro)

#### US-014: Automated Weekly Summary

```gherkin
Feature: Weekly Roast Report

  Scenario: Weekly report delivery
    Given Pro пользователь с данными за неделю
    When наступает понедельник 10:00 MSK
    Then бот отправляет саркастичный weekly summary:
      - Общая сумма расходов за неделю
      - Топ-3 категории
      - "Победа недели" (наименьшая категория / экономия)
      - "Позор недели" (наибольший перерасход)
      - Challenge of the Week предложение
    And длина: 200-400 символов
    And тон: текущий mode пользователя (Roast/Hype/Serious)
```

### 1.11 Feature: AI Savings Plan (Pro)

#### US-015: Personalized Savings Recommendations

```gherkin
Feature: AI Savings Plan

  Scenario: Generate savings plan
    Given Pro пользователь с >= 1 месяцем данных
    When запрашивает /savings
    Then AI анализирует паттерны расходов
    And генерирует 3-5 конкретных рекомендаций:
      | # | Рекомендация | Потенциальная экономия |
      | 1 | Сократить Яндекс.Еду на 30% | ~2,500₽/мес |
      | 2 | Отменить подписку X (не используешь) | 599₽/мес |
      | 3 | Такси → метро 2 раза в неделю | ~1,200₽/мес |
    And показывает total потенциальной экономии
    And тон зависит от режима (roast: "Если перестанешь кормить Delivery Club...")
```

### 1.12 Feature: Subscription & Payments

#### US-016: Subscribe to Pro/Premium

```gherkin
Feature: Subscription

  Scenario: Subscribe to Pro
    Given Free пользователь
    When отправляет /subscribe или нажимает upsell button
    Then бот показывает:
      """
      🔥 Pro — 399₽/мес (3,499₽/год)
      ✅ Безлимит прожарок
      ✅ Weekly Roast Report
      ✅ Challenges + Badges
      ✅ Подписки-детектор
      ✅ Pie-charts расходов
      ✅ AI план экономии
      [Оформить Pro] [Сравнить с Premium]
      """
    And при нажатии [Оформить] — redirect на payment page (YooKassa)

  Scenario: Subscription confirmation
    Given пользователь оплатил подписку
    When webhook от YooKassa подтверждает оплату
    Then user.tier обновляется на "pro" или "premium"
    And бот: "Добро пожаловать в Pro! Теперь прожарки без лимитов 🔥"
    And разблокируются Pro фичи

  Scenario: Cancel subscription
    Given пользователь на Pro/Premium
    When отправляет /cancel
    Then бот показывает: "Подожди! За этот месяц ты сэкономил X₽ благодаря нам"
    And показывает [Всё равно отменить] [Остаться] [Заморозить на 1 мес]
    And если отменяет — подписка действует до конца оплаченного периода
```

### 1.13 Feature: Data Privacy & Deletion

#### US-017: GDPR-like Data Control (152-ФЗ)

```gherkin
Feature: Data Privacy

  Scenario: Delete all user data
    Given пользователь отправляет /delete
    Then бот предупреждает: "Это удалит ВСЕ твои данные безвозвратно. Уверен?"
    And показывает [Да, удалить всё] [Отмена]
    When пользователь подтверждает
    Then удаляются: транзакции, категории, настройки, subscription (refund policy)
    And бот: "Все данные удалены. Буду скучать 😢"
    And user record помечается как deleted (soft delete для audit)

  Scenario: Export user data
    Given пользователь отправляет /export
    Then бот генерирует CSV файл со всеми транзакциями пользователя
    And отправляет файл в чат
    And: "Вот все твои данные. Формат: CSV"
```

### 1.14 Feature: Churn Prevention

#### US-018: Re-engagement Messages

```gherkin
Feature: Churn Prevention

  Scenario: 5-day inactivity warning
    Given пользователь не взаимодействовал 5 дней
    Then бот отправляет: "Эй, ты там не обанкротился? Кинь выписку 👀"

  Scenario: 14-day inactivity — churning
    Given пользователь не взаимодействовал 14 дней
    Then бот отправляет: "Без меня ты уже потратил ~X₽ на ерунду. Вернись!"
    And X рассчитывается на основе среднего за предыдущие периоды

  Scenario: Subscription cancellation save
    Given пользователь инициировал отмену подписки
    Then бот показывает value delivered: "За этот месяц: сэкономил X₽, выполнил Y challenges"
    And предлагает [Заморозить на 1 мес бесплатно] как альтернативу

  Scenario: Churned user exit survey
    Given подписка отменена
    Then бот отправляет 1 вопрос: "Почему уходишь? [Дорого] [Неинтересно] [Нашёл другое] [Другое]"
    And сохраняет ответ для analytics
```

---

## 2. Non-Functional Requirements

### 2.1 Performance

| Metric | Requirement | Measurement |
|--------|-------------|-------------|
| Bot response time (p50) | < 2s | Telegram message → first response |
| Bot response time (p99) | < 5s | Including LLM generation |
| OCR processing | < 10s | Photo upload → structured result |
| Roast generation | < 5s | /roast → complete response |
| Roast card generation | < 3s | Button click → image sent |
| CSV processing | < 5s | File upload → parsed result |
| API uptime | 99.5% (MVP) | Monthly measurement |
| Concurrent users | 1,000 | Simultaneous active sessions |

### 2.2 Security

| Requirement | Implementation |
|-------------|----------------|
| Authentication | Telegram user_id + bot token verification |
| Encryption at rest | AES-256 (PostgreSQL TDE + disk encryption) |
| Encryption in transit | TLS 1.3 for all connections |
| PII handling | No raw PII sent to LLM APIs. Only aggregated categories + amounts |
| 152-ФЗ compliance | User consent on /start, data localization (RU servers), /delete, /export |
| Rate limiting | 60 messages/minute per user, 10 uploads/hour |
| Input validation | All user inputs sanitized, file size limits, format validation |
| Secret management | Environment variables, no secrets in code |

### 2.3 Scalability

| Phase | Users | Transactions/day | Infra |
|-------|-------|-------------------|-------|
| MVP (M1-3) | 2K | 5K | 1 VPS (4 vCPU, 8GB RAM) |
| Growth (M4-6) | 25K | 60K | 2 VPS + DB replica |
| Scale (M7-12) | 100K | 250K | 3+ VPS + DB cluster |
| Expand (M13-24) | 320K | 800K | Auto-scaling VPS cluster |

### 2.4 Reliability

| Metric | Requirement |
|--------|-------------|
| Uptime SLA | 99.5% (MVP), 99.9% (M12+) |
| RTO | 1 hour |
| RPO | 15 minutes |
| Backup frequency | Every 6 hours (DB), daily (full) |
| Failover | LLM multi-provider, DB replica |

### 2.5 Localization

| Requirement | Detail |
|-------------|--------|
| Language | Russian only (MVP) |
| Currency | RUB (₽) only (MVP) |
| Timezone | MSK (UTC+3) default, user-configurable |
| Date format | DD.MM.YYYY |
| Number format | 1 000,00₽ (space as thousands separator) |

---

## 3. Constraints & Assumptions

### Constraints

| ID | Constraint | Impact |
|----|-----------|--------|
| C-001 | No Open Banking API until 2027 | Screenshot OCR + CSV only |
| C-002 | No МФО license | Cannot offer lending/cash advance |
| C-003 | LLM API access may require proxy from Russia | Multi-provider failover needed |
| C-004 | VPS infrastructure (no managed K8s) | Docker Compose deploy |
| C-005 | 3-person team (M1-6) | Must prioritize ruthlessly |
| C-006 | 152-ФЗ data localization | All data on Russian servers |

### Assumptions

| ID | Assumption | Risk if Wrong | Validation |
|----|-----------|---------------|------------|
| A-001 | Russian Gen Z will respond to Roast Mode | High — core value prop fails | MVP beta testing |
| A-002 | Screenshot OCR accuracy >= 80% for top-5 banks | High — data input broken | Technical POC |
| A-003 | Free → Paid conversion >= 6% | Critical — business model fails | M3-6 metrics |
| A-004 | Monthly churn <= 7% | High — LTV calculations off | M6+ cohort analysis |
| A-005 | CAC via viral Telegram <= 250₽ | Medium — growth economics | M1-3 tracking |
| A-006 | GPT-4o accessible from Russia (via proxy) | High — core AI broken | Technical POC |
