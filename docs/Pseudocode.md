# Pseudocode — ФинРоуст
**Version:** 1.0 | **Дата:** 2026-04-09

---

## 1. Core Data Structures

### 1.1 Domain Entities

```
ENTITY User:
    id: UUID (primary key)
    telegram_id: BigInt (unique, indexed)
    username: String (nullable)
    display_name: String
    tier: Enum [FREE, PRO, PREMIUM] (default: FREE)
    roast_level: Int (1-5, default: 3)
    tone_mode: Enum [ROAST, HYPE, SERIOUS] (default: ROAST)
    locale: String (default: "ru")
    timezone: String (default: "Europe/Moscow")
    streak_days: Int (default: 0)
    streak_last_date: Date (nullable)
    created_at: Timestamp
    updated_at: Timestamp
    deleted_at: Timestamp (nullable, soft delete)

ENTITY Transaction:
    id: UUID (primary key)
    user_id: UUID (FK → User)
    amount: Decimal (positive = expense, negative = income)
    currency: String (default: "RUB")
    description: String
    category_id: UUID (FK → Category)
    category_confidence: Float (0-1)
    source: Enum [OCR, CSV, MANUAL]
    original_text: String (raw OCR/CSV text)
    transaction_date: Date
    created_at: Timestamp

ENTITY Category:
    id: UUID (primary key)
    name: String (unique)
    emoji: String
    parent_id: UUID (nullable, FK → Category, for subcategories)

    STATIC CATEGORIES:
        food_delivery: "🍔 Доставка еды"
        food_grocery: "🛒 Продукты"
        food_restaurant: "🍽️ Рестораны/Кафе"
        transport_taxi: "🚕 Такси"
        transport_public: "🚇 Общественный транспорт"
        entertainment: "🎮 Развлечения"
        subscriptions: "📱 Подписки"
        shopping_clothes: "👕 Одежда"
        shopping_marketplace: "📦 Маркетплейсы"
        health: "💊 Здоровье"
        utilities: "🏠 ЖКХ"
        transfer: "💸 Переводы"
        income: "💰 Доход"
        other: "📋 Прочее"

ENTITY Subscription:
    id: UUID (primary key)
    user_id: UUID (FK → User)
    merchant: String
    amount: Decimal
    frequency: Enum [WEEKLY, MONTHLY, YEARLY]
    detected_at: Timestamp
    status: Enum [ACTIVE, CANCELLED, PAUSED]
    last_charge_date: Date

ENTITY Challenge:
    id: UUID (primary key)
    user_id: UUID (FK → User)
    type: String (challenge template id)
    description: String
    target_value: Decimal
    current_value: Decimal
    status: Enum [ACTIVE, COMPLETED, FAILED, SKIPPED]
    started_at: Date
    ends_at: Date

ENTITY RoastCard:
    id: UUID (primary key)
    user_id: UUID (FK → User)
    roast_text: String
    image_url: String
    hide_amounts: Boolean (default: false)
    share_count: Int (default: 0)
    created_at: Timestamp

ENTITY UserSubscription:
    id: UUID (primary key)
    user_id: UUID (FK → User)
    tier: Enum [PRO, PREMIUM]
    payment_provider: String (e.g., "yookassa")
    payment_id: String
    started_at: Timestamp
    expires_at: Timestamp
    auto_renew: Boolean (default: true)
    cancelled_at: Timestamp (nullable)
```

### 1.2 State Machines

```
USER ONBOARDING STATE:
    NEW → WELCOMED → DATA_UPLOADED → FIRST_ROAST → ACTIVATED
    
    Transitions:
        NEW → WELCOMED: on /start received
        WELCOMED → DATA_UPLOADED: on first successful upload (OCR/CSV/manual)
        DATA_UPLOADED → FIRST_ROAST: on first /roast completed
        FIRST_ROAST → ACTIVATED: on first share OR second session

SUBSCRIPTION STATE:
    FREE → TRIAL → ACTIVE → GRACE → EXPIRED → CANCELLED
    
    Transitions:
        FREE → ACTIVE: on payment confirmed
        ACTIVE → GRACE: on payment failed (retry for 3 days)
        GRACE → ACTIVE: on retry payment success
        GRACE → EXPIRED: after 3 failed retries
        ACTIVE → CANCELLED: on user cancellation (access until period end)
        EXPIRED → ACTIVE: on re-subscription
        CANCELLED → ACTIVE: on re-subscription
```

---

## 2. Core Algorithms

### 2.1 Screenshot OCR Pipeline

```
FUNCTION processScreenshot(photo: TelegramPhoto, user: User) → TransactionList:
    INPUT: photo (Telegram file reference), user
    OUTPUT: list of Transaction objects

    1. Download photo from Telegram API → raw_image (bytes)
    2. Preprocess image:
        a. Convert to grayscale
        b. Apply adaptive thresholding
        c. Deskew if rotation detected (angle > 2°)
        d. Increase contrast for better OCR
    3. Run OCR:
        a. primary: Tesseract (rus+eng language pack)
        b. IF confidence < 70% → fallback: Yandex Vision API
        c. result: raw_text + confidence_score
    4. IF confidence < 50%:
        RETURN Error("LOW_OCR_CONFIDENCE", "Не могу разобрать фото")
    5. Detect bank format:
        a. Search for bank identifiers in raw_text:
            - "Сбербанк" / "Sber" → format = SBERBANK
            - "Тинькофф" / "Tinkoff" → format = TINKOFF
            - "Альфа" / "Alfa" → format = ALFABANK
            - "ВТБ" / "VTB" → format = VTB
            - ELSE → format = UNKNOWN
    6. Parse transactions based on format:
        a. FOR each format: apply regex patterns for date, amount, description
        b. SBERBANK pattern: /(\d{2}\.\d{2}\.\d{2,4})\s+(.+?)\s+([+-]?\d[\d\s]*[,.]?\d{0,2})\s*₽?/
        c. Handle multi-line transaction descriptions
    7. FOR each parsed transaction:
        a. Normalize amount (remove spaces, convert comma to dot)
        b. Parse date to ISO format
        c. Clean description (trim, remove extra spaces)
        d. Categorize via AI (see categorizeTransaction)
        e. Create Transaction entity
    8. RETURN list of Transaction entities

FUNCTION categorizeTransaction(description: String, amount: Decimal) → (Category, confidence):
    INPUT: transaction description text, amount
    OUTPUT: category + confidence score

    1. Check rule-based mapping first (fast path):
        - "Яндекс.Еда" / "Delivery Club" / "СберМаркет" → food_delivery (0.95)
        - "Яндекс.Такси" / "Uber" / "Ситимобил" → transport_taxi (0.95)
        - "Wildberries" / "OZON" / "AliExpress" → shopping_marketplace (0.95)
        - "Spotify" / "YouTube" / "Netflix" / "Кинопоиск" → subscriptions (0.95)
        - etc. (50+ rules for common Russian merchants)
    2. IF no rule match:
        a. Send to LLM with prompt:
            """
            Категоризируй транзакцию. Ответь ТОЛЬКО названием категории.
            Описание: {description}
            Сумма: {amount}₽
            Категории: [список категорий]
            """
        b. Parse LLM response → category
        c. confidence = 0.75 (LLM-based)
    3. RETURN (category, confidence)
```

### 2.2 CSV Parser

```
FUNCTION parseCSV(file: TelegramDocument, user: User) → TransactionList:
    INPUT: CSV file from Telegram
    OUTPUT: list of Transaction objects

    1. Download file from Telegram API → raw_bytes
    2. Detect encoding: try [utf-8, windows-1251, cp866] → decoded_text
    3. Detect delimiter: count occurrences of [',', ';', '\t'] in first 5 lines → delimiter
    4. Parse CSV headers
    5. Detect bank format:
        a. Match header patterns:
            - SBERBANK: "Дата операции", "Сумма операции", "Описание"
            - TINKOFF: "Дата операции", "Сумма платежа", "Описание"
            - ALFABANK: "Дата", "Сумма", "Описание операции"
            - UNIVERSAL: columns containing "дата"/"date", "сумма"/"amount", "описание"/"description"
    6. IF format not detected:
        a. Ask user to map columns interactively
        b. RETURN AskUserMapping(headers)
    7. FOR each row:
        a. Extract date, amount, description based on detected format
        b. Parse date (handle formats: DD.MM.YYYY, DD.MM.YY, YYYY-MM-DD)
        c. Parse amount (handle: "1 234,56", "-1234.56", "1234,56-")
        d. Categorize transaction
        e. Create Transaction entity
    8. RETURN list of Transaction entities
```

### 2.3 Roast Generation

```
FUNCTION generateRoast(user: User, transactions: TransactionList) → RoastResult:
    INPUT: user with settings, list of recent transactions
    OUTPUT: roast text + metadata

    1. Aggregate transactions by category:
        spending = GROUP transactions BY category
        SORT spending BY total_amount DESC
        top_categories = spending[0:3]
    2. Calculate insights:
        total_spent = SUM(transactions.amount WHERE amount > 0)
        avg_daily = total_spent / period_days
        biggest_single = MAX(transactions.amount)
        subscription_total = SUM(WHERE category = subscriptions)
    3. Check roast eligibility:
        IF len(transactions) < 5:
            RETURN Error("INSUFFICIENT_DATA")
        IF user.tier == FREE AND user.roasts_this_week >= 1:
            RETURN Error("FREE_LIMIT_REACHED")
    4. Build LLM prompt:
        system_prompt = loadPersonalityPrompt(user.tone_mode, user.roast_level)
        user_prompt = """
        Данные пользователя:
        - Топ категории: {top_categories with amounts}
        - Общая сумма: {total_spent}₽ за {period_days} дней
        - Самая большая трата: {biggest_single}₽ ({description})
        - Подписки: {subscription_total}₽/мес
        
        Сгенерируй {tone_mode} анализ расходов.
        Правила:
        - Используй русский мем-юмор и сленг
        - Упомяни конкретные категории и суммы
        - Длина: 100-300 символов
        - Закончи 1 конструктивным советом
        - roast_level: {user.roast_level}/5
        - НЕ используй нецензурную лексику
        - НЕ давай конкретных финансовых рекомендаций (disclaimer)
        """
    5. Call LLM provider:
        response = llmProvider.generate(system_prompt, user_prompt)
        IF response.error → try next provider in failover chain
    6. Post-process:
        a. Check content safety (no profanity, no PII leak)
        b. Add disclaimer if needed
        c. Truncate if > 400 chars
    7. Save roast to history
    8. Increment user.roasts_this_week
    9. RETURN RoastResult(text=response, categories=top_categories, insights=insights)

FUNCTION loadPersonalityPrompt(mode: ToneMode, level: Int) → String:
    base = """
    Ты — ФинРоуст, AI финансовый коуч. Ты дерзкий друг, который помогает 
    молодёжи следить за деньгами через юмор. Ты никогда не оскорбляешь 
    личность — только финансовые привычки. Ты заботишься о пользователе.
    """
    
    IF mode == ROAST:
        tone = ROAST_TONES[level]  // от мягкого (1) до жёсткого (5)
    ELIF mode == HYPE:
        tone = "Мотивируй, поздравляй, подчёркивай позитивное. Будь энергичным."
    ELIF mode == SERIOUS:
        tone = "Нейтральный аналитический тон. Факты, цифры, конкретные советы."
    
    RETURN base + tone
```

### 2.4 Roast Card Image Generation

```
FUNCTION generateRoastCard(roast: RoastResult, user: User, hideAmounts: Boolean) → Image:
    INPUT: roast result, user preferences, privacy flag
    OUTPUT: 1080x1080 PNG image

    1. Select template based on tone_mode:
        - ROAST: dark background, fire emoji, red/orange gradient
        - HYPE: bright background, star emoji, green/gold gradient
        - SERIOUS: minimal, dark blue, clean typography
    2. Compose layers:
        a. Background: gradient template (1080x1080)
        b. Roast text: main font, 36-48px, white, center-aligned
           - Auto-wrap at 25 chars per line
           - IF text > 200 chars: reduce font to 32px
        c. Category breakdown:
           FOR each top_category in roast.categories:
               - Emoji + name + (hideAmounts ? "**%" : "X,XXX₽")
               - Horizontal bar chart proportional to amount
        d. Watermark: "@FinRoastBot" + QR code (bottom-right)
        e. Branding: ФинРоуст logo (top-left, subtle)
    3. Render to PNG (1080x1080, 72dpi)
    4. Upload to object storage → image_url
    5. Save RoastCard entity
    6. RETURN image
```

### 2.5 Subscription Detection

```
FUNCTION detectSubscriptions(user: User) → List[Subscription]:
    INPUT: user with transaction history
    OUTPUT: list of detected recurring payments

    1. Load all transactions for user (last 3 months)
    2. Group by merchant (normalized description):
        a. Normalize: lowercase, remove digits, trim
        b. Group by similarity (Levenshtein distance < 3)
    3. FOR each merchant group:
        a. IF count >= 2 AND amounts are similar (within 10%):
            b. Calculate frequency:
                - avg_interval = mean(diff(sorted dates))
                - IF 25 <= avg_interval <= 35 → MONTHLY
                - IF 6 <= avg_interval <= 8 → WEEKLY
                - IF 355 <= avg_interval <= 375 → YEARLY
            c. IF frequency detected:
                - Create Subscription entity
                - amount = median(amounts)
                - status = ACTIVE
    4. Compare with existing subscriptions:
        - New subscriptions: notify user
        - Missing subscriptions: mark as possibly cancelled
    5. RETURN list of Subscription entities
```

### 2.6 Streak & Challenge Tracking

```
FUNCTION updateStreak(user: User) → StreakResult:
    INPUT: user
    OUTPUT: updated streak info

    1. today = current_date(user.timezone)
    2. IF user.streak_last_date == today:
        RETURN StreakResult(days=user.streak_days, changed=false)
    3. IF user.streak_last_date == today - 1:
        user.streak_days += 1
        user.streak_last_date = today
        milestone = checkMilestone(user.streak_days)  // 7, 14, 30, 60, 100
        RETURN StreakResult(days=user.streak_days, changed=true, milestone=milestone)
    4. ELSE:
        old_streak = user.streak_days
        user.streak_days = 1
        user.streak_last_date = today
        RETURN StreakResult(days=1, changed=true, broken=true, previous=old_streak)
    5. Save user

FUNCTION evaluateChallenge(challenge: Challenge) → ChallengeResult:
    INPUT: active challenge
    OUTPUT: status update

    1. Load transactions for challenge period
    2. Calculate current_value based on challenge type:
        - SPEND_LESS_CATEGORY: sum(WHERE category = target AND date in range)
        - NO_SPEND_CATEGORY: count(WHERE category = target AND date in range)
        - SAVE_AMOUNT: total_income - total_expense in range
    3. challenge.current_value = current_value
    4. IF today > challenge.ends_at:
        IF current_value <= challenge.target_value:
            challenge.status = COMPLETED
            award badge
        ELSE:
            challenge.status = FAILED
    5. Save challenge
    6. RETURN ChallengeResult(status, progress=current_value/target_value)
```

---

## 3. API Contracts

### 3.1 Telegram Bot Webhook

```
ENDPOINT: POST /webhook/telegram
    INPUT: Telegram Update object (JSON)
    
    PROCESS:
    1. Verify update signature (bot token)
    2. Extract message/callback_query
    3. Route to handler:
        - /start → handleStart(user)
        - /roast → handleRoast(user)
        - /hype → handleToneSwitch(user, HYPE)
        - /serious → handleToneSwitch(user, SERIOUS)
        - /stats → handleStats(user)
        - /challenge → handleChallenge(user)
        - /streak → handleStreak(user)
        - /subscriptions → handleSubscriptions(user)
        - /savings → handleSavings(user)
        - /settings → handleSettings(user)
        - /subscribe → handleSubscribe(user)
        - /cancel → handleCancel(user)
        - /delete → handleDelete(user)
        - /export → handleExport(user)
        - /help → handleHelp(user)
        - photo → handlePhotoUpload(user, photo)
        - document(.csv) → handleCSVUpload(user, document)
        - text → handleFreeText(user, text)
        - callback_query → handleCallback(user, callback)
    4. RETURN 200 OK (always, per Telegram requirements)
```

### 3.2 Payment Webhook

```
ENDPOINT: POST /webhook/payment
    INPUT: YooKassa notification (JSON)
    
    PROCESS:
    1. Verify signature (YooKassa secret key)
    2. Parse event type:
        - payment.succeeded → activateSubscription(user, payment)
        - payment.canceled → handlePaymentFailed(user, payment)
        - refund.succeeded → handleRefund(user, refund)
    3. Update user subscription status
    4. Send confirmation/error message to Telegram
    5. RETURN 200 OK
```

### 3.3 Internal Service API

```
SERVICE: LLMProvider
    
    FUNCTION generate(systemPrompt: String, userPrompt: String) → LLMResponse:
        providers = [GPT4O, CLAUDE, YANDEXGPT, GIGACHAT]
        
        FOR provider in providers:
            TRY:
                response = provider.chat(
                    model = provider.default_model,
                    messages = [
                        {role: "system", content: systemPrompt},
                        {role: "user", content: userPrompt}
                    ],
                    max_tokens = 500,
                    temperature = 0.8  // creativity for roasts
                )
                RETURN LLMResponse(text=response.content, provider=provider.name)
            CATCH (RateLimitError, TimeoutError, APIError):
                log.warn("Provider {provider} failed, trying next")
                CONTINUE
        
        RETURN LLMResponse(error="ALL_PROVIDERS_FAILED")

SERVICE: OCRProvider

    FUNCTION extractText(image: bytes) → OCRResult:
        // Primary: Tesseract (self-hosted)
        TRY:
            text = tesseract.image_to_string(image, lang="rus+eng")
            confidence = tesseract.image_to_data(image).mean_confidence
            IF confidence >= 70%:
                RETURN OCRResult(text=text, confidence=confidence, provider="tesseract")
        CATCH: pass
        
        // Fallback: Yandex Vision
        TRY:
            result = yandexVision.recognize(image)
            RETURN OCRResult(text=result.text, confidence=result.confidence, provider="yandex")
        CATCH: pass
        
        RETURN OCRResult(error="OCR_FAILED")
```

### 3.4 Scheduled Jobs (Cron)

```
CRON JOBS:

    weekly_roast_report:
        schedule: "0 10 * * 1"  // Monday 10:00 MSK
        action:
            FOR user in users WHERE tier IN [PRO, PREMIUM] AND has_data_this_week:
                report = generateWeeklyReport(user)
                sendTelegramMessage(user.telegram_id, report)

    weekly_challenge:
        schedule: "0 10 * * 1"  // Monday 10:00 MSK
        action:
            FOR user in users WHERE is_active:
                // Close previous challenge
                evaluateChallenge(user.active_challenge)
                // Generate new challenge
                challenge = generateChallenge(user)
                sendTelegramMessage(user.telegram_id, challenge)

    churn_prevention:
        schedule: "0 12 * * *"  // Daily 12:00 MSK
        action:
            FOR user in users WHERE is_active:
                days_inactive = today - user.last_activity_date
                IF days_inactive == 5:
                    sendReengagement(user, "5_DAY")
                ELIF days_inactive == 14:
                    sendReengagement(user, "14_DAY")

    monthly_grade:
        schedule: "0 10 1 * *"  // 1st of month, 10:00 MSK
        action:
            FOR user in users WHERE has_data_last_month:
                grade = calculateMonthlyGrade(user)
                sendTelegramMessage(user.telegram_id, grade)

    subscription_renewal:
        schedule: "0 */6 * * *"  // Every 6 hours
        action:
            FOR sub in subscriptions WHERE expires_at < now + 1_day AND auto_renew:
                TRY: renewPayment(sub)
                CATCH: markGracePeriod(sub)

    streak_reset:
        schedule: "0 0 * * *"  // Midnight MSK
        action:
            FOR user in users WHERE streak_last_date < today - 1 AND streak_days > 0:
                // Don't reset here — reset lazily on next interaction
                // This job is for metrics/reporting only
                log.info("Streak will break for user {user.id}")
```

---

## 4. Error Handling

```
ERROR CODES:

    // Input errors (4xx)
    E_INPUT_001: "Invalid photo format" → "Поддерживаю jpg и png. Попробуй другой формат"
    E_INPUT_002: "File too large" → "Файл слишком большой. Максимум 10MB"
    E_INPUT_003: "Invalid CSV format" → "Не могу разобрать CSV. Проверь формат"
    E_INPUT_004: "No transactions found" → "Не нашёл транзакций. Попробуй другую выписку"
    
    // OCR errors
    E_OCR_001: "Low confidence" → "Не могу разобрать фото. Попробуй фото получше"
    E_OCR_002: "Unsupported bank" → "Этот банк пока не поддерживаю. Кинь CSV"
    E_OCR_003: "OCR service unavailable" → "Сервис распознавания недоступен. Попробуй через 5 мин"
    
    // LLM errors
    E_LLM_001: "All providers failed" → "AI на перекуре. Попробуй через пару минут ☕"
    E_LLM_002: "Content filtered" → (silent retry with adjusted prompt)
    E_LLM_003: "Rate limited" → "Слишком много запросов. Подожди минутку"
    
    // Business logic errors
    E_BIZ_001: "Free limit reached" → "Лимит прожарок на этой неделе. Хочешь безлимит?"
    E_BIZ_002: "Insufficient data" → "Маловато данных. Кинь ещё выписку"
    E_BIZ_003: "Subscription expired" → "Подписка истекла. Хочешь продлить?"
    
    // System errors (5xx)
    E_SYS_001: "Database error" → "Технические работы. Попробуй через 5 минут"
    E_SYS_002: "Storage error" → "Не могу сохранить данные. Попробуй ещё раз"

GLOBAL ERROR HANDLER:
    1. Log error with context (user_id, action, error_code, stack_trace)
    2. Increment error counter metric
    3. Send user-friendly message (never expose internal details)
    4. IF critical (DB down, all LLMs down):
        a. Alert ops team (Telegram admin channel)
        b. Enable maintenance mode
```
