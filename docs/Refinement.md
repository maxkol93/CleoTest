# Refinement — ФинРоуст
**Version:** 1.0 | **Дата:** 2026-04-09

---

## 1. Edge Cases & Error Handling

### 1.1 OCR Edge Cases

| # | Edge Case | Detection | Handling |
|---|-----------|-----------|----------|
| EC-001 | Screenshot contains multiple bank statements | Multiple bank identifiers detected | Process only first detected, warn user |
| EC-002 | Screenshot is a credit card statement (not debit) | Negative balances, "кредитный лимит" | Parse normally, flag as credit transactions |
| EC-003 | Screenshot in dark mode | Low contrast detection | Apply inverse + contrast enhancement before OCR |
| EC-004 | Screenshot with notifications bar overlapping | Text truncated at top | Crop top 10% if notification patterns detected |
| EC-005 | Multiple currencies in one statement | Non-RUB amounts detected | Convert to RUB at daily rate, mark as converted |
| EC-006 | Very old statement (> 1 year) | Date parsing shows > 12 months ago | Accept but warn: "Данные за [дата]. Всё верно?" |
| EC-007 | Duplicate upload (same statement) | Hash of extracted text matches existing | "Эту выписку я уже видел. Кинь новую!" |
| EC-008 | Screenshot of another user's statement | Cannot detect | Process normally (privacy is user's responsibility) |

### 1.2 LLM Edge Cases

| # | Edge Case | Detection | Handling |
|---|-----------|-----------|----------|
| EC-009 | LLM generates offensive content | Keyword filter + sentiment analysis | Regenerate with higher safety prompt; if fails 2x → fallback template |
| EC-010 | LLM generates financial advice (liability) | Keywords: "инвестируй", "купи акции", "возьми кредит" | Auto-add disclaimer: "Это не финансовая рекомендация" |
| EC-011 | LLM response too long (> 400 chars) | Length check | Truncate at last sentence before limit |
| EC-012 | LLM response in wrong language | Language detection | Retry with explicit "Отвечай на русском" in prompt |
| EC-013 | LLM hallucinates spending data | Cross-check amounts in response vs actual data | Only include verified amounts from database |
| EC-014 | All LLM providers down simultaneously | All 4 providers fail | Send pre-written template roast with user's top category |

### 1.3 Business Logic Edge Cases

| # | Edge Case | Detection | Handling |
|---|-----------|-----------|----------|
| EC-015 | User has only income, no expenses | All transactions negative (income) | "Ты только зарабатываешь и не тратишь? Подозрительно... 🤔" |
| EC-016 | User uploads empty statement | 0 transactions parsed | "Пустая выписка? Или ты настолько экономный?" |
| EC-017 | Extremely high single transaction (> 500K₽) | Amount threshold | Flag for review, exclude from roast unless confirmed |
| EC-018 | User rapidly switches tone modes | > 5 switches per minute | Ignore rapid switches, rate limit to 1/min |
| EC-019 | User on Free requests Pro feature | Tier check on each Pro/Premium feature | Upsell message with specific value proposition |
| EC-020 | Payment succeeds but webhook fails | Periodic reconciliation job | Check YooKassa API for unconfirmed payments every hour |
| EC-021 | User deletes data mid-challenge | Challenge references deleted transactions | Auto-fail challenge, notify user |
| EC-022 | Timezone edge case (user changes timezone) | Timezone setting change | Recalculate streak and challenge dates from new timezone |

### 1.4 Concurrency Edge Cases

| # | Edge Case | Detection | Handling |
|---|-----------|-----------|----------|
| EC-023 | User sends multiple photos simultaneously | Multiple OCR tasks for same user | Queue with user lock; process sequentially per user |
| EC-024 | Double payment submission | Duplicate payment_id | Idempotent payment processing; check payment_id uniqueness |
| EC-025 | Concurrent roast requests | Multiple /roast from same user | Redis lock per user; return "Подожди, генерирую..." |

---

## 2. Test Specifications

### 2.1 Unit Tests

| Test ID | Module | Description | Input | Expected Output |
|---------|--------|-------------|-------|-----------------|
| UT-001 | OCR | Parse Sberbank screenshot | Mock Sberbank image | List of 5+ transactions |
| UT-002 | OCR | Handle low confidence | Blurry image mock | Error "LOW_OCR_CONFIDENCE" |
| UT-003 | CSV | Parse Sberbank CSV | Valid CSV file | List of transactions |
| UT-004 | CSV | Handle wrong encoding | CP866 CSV | Correct decoded transactions |
| UT-005 | CSV | Detect unknown format | Random CSV | AskUserMapping response |
| UT-006 | Categorizer | Rule-based categorization | "Яндекс.Еда 1234₽" | Category: food_delivery, conf: 0.95 |
| UT-007 | Categorizer | LLM-based categorization | "ООО Ромашка 500₽" | Category with conf >= 0.7 |
| UT-008 | Roast Engine | Generate roast (Roast Mode) | User + 10 transactions | Text 100-300 chars, contains top categories |
| UT-009 | Roast Engine | Generate roast (Hype Mode) | User(tone=HYPE) + transactions | Positive tone text |
| UT-010 | Roast Engine | Free limit enforcement | Free user, 1 roast used this week | Error "FREE_LIMIT_REACHED" |
| UT-011 | Roast Engine | Insufficient data | User + 3 transactions | Error "INSUFFICIENT_DATA" |
| UT-012 | Card Generator | Generate 1080x1080 card | Roast text + categories | PNG image, correct dimensions |
| UT-013 | Card Generator | Hide amounts flag | hideAmounts=true | No numeric amounts in image text |
| UT-014 | Subscription Detector | Detect monthly subscription | 3 months of same-amount transactions | Subscription entity |
| UT-015 | Subscription Detector | No false positive on salary | Monthly income | No subscription detected |
| UT-016 | Streak | Consecutive days | streak_last=yesterday | streak_days + 1 |
| UT-017 | Streak | Broken streak | streak_last=3 days ago | streak_days = 1 |
| UT-018 | Streak | Same day interaction | streak_last=today | No change |
| UT-019 | Challenge | Complete challenge | current <= target, past end date | status = COMPLETED |
| UT-020 | Challenge | Fail challenge | current > target, past end date | status = FAILED |
| UT-021 | LLM Provider | Failover to next provider | Primary timeout | Response from secondary provider |
| UT-022 | LLM Provider | All providers fail | All 4 timeout | Error "ALL_PROVIDERS_FAILED" |
| UT-023 | Payment | Idempotent processing | Duplicate payment_id | No duplicate subscription created |
| UT-024 | Manual Input | Parse "кофе 350" | Text "кофе 350" | Transaction(category=cafe, amount=350) |
| UT-025 | Manual Input | Parse "вчера такси 890р" | Text with date | Transaction(date=yesterday, amount=890) |

### 2.2 Integration Tests

| Test ID | Scenario | Steps | Expected Result |
|---------|----------|-------|-----------------|
| IT-001 | Full onboarding flow | 1. /start → 2. Upload screenshot → 3. /roast → 4. Share | User state: ACTIVATED, roast card generated |
| IT-002 | CSV upload → analytics | 1. Upload CSV → 2. /stats | Category breakdown displayed |
| IT-003 | Subscription flow | 1. /subscribe → 2. Payment webhook → 3. /roast unlimited | Tier upgraded, no limit on roasts |
| IT-004 | LLM failover chain | 1. Mock GPT-4o failure → 2. Call roast | Response from Claude (fallback) |
| IT-005 | Weekly report cron | 1. Seed transactions → 2. Trigger cron | Pro user receives weekly report |
| IT-006 | Challenge lifecycle | 1. Create challenge → 2. Add transactions → 3. Evaluate | Challenge completed/failed based on data |
| IT-007 | Data deletion | 1. /delete → 2. Confirm | All user data removed from DB |
| IT-008 | Rate limiting | 1. Send 61 messages in 1 minute | 61st message rate limited |
| IT-009 | Payment webhook retry | 1. Fail webhook → 2. Retry | Payment eventually processed |
| IT-010 | Concurrent uploads | 1. Send 3 photos simultaneously | Processed sequentially, all saved |

### 2.3 End-to-End Tests

| Test ID | Scenario | Description |
|---------|----------|-------------|
| E2E-001 | New user to first roast | Full flow from /start to receiving first roast card |
| E2E-002 | Free → Pro upgrade | User hits free limit, upgrades, gets unlimited access |
| E2E-003 | Weekly engagement cycle | Monday report → accept challenge → mid-week update → result |
| E2E-004 | Churn prevention | 5-day inactivity → re-engagement message → user returns |
| E2E-005 | Data export & delete | User exports CSV → deletes all data → verifies empty |

---

## 3. Performance Optimization

### 3.1 Database Optimization

| Optimization | Description | Expected Impact |
|-------------|-------------|-----------------|
| Connection pooling | SQLAlchemy async pool, min=5, max=20 | Prevent connection exhaustion |
| Index on transactions(user_id, date) | Composite index for user's recent transactions | 10x faster analytics queries |
| Partitioning (M12+) | Partition transactions by month | Query only relevant partition |
| Read replica (M6+) | Add PostgreSQL replica for read queries | Reduce primary load by 60% |
| Query caching (Redis) | Cache category breakdown for 5 min | Reduce DB load for /stats |

### 3.2 LLM Optimization

| Optimization | Description | Expected Impact |
|-------------|-------------|-----------------|
| Prompt caching | Cache similar spending pattern prompts | 30% cost reduction |
| Template roasts | Pre-generate roasts for common patterns | < 1s response for cached |
| Batch categorization | Categorize all transactions in one LLM call | 80% fewer API calls |
| Streaming responses | Stream LLM output to Telegram | Perceived < 2s response |

### 3.3 OCR Optimization

| Optimization | Description | Expected Impact |
|-------------|-------------|-----------------|
| Image preprocessing pipeline | Grayscale + threshold + deskew | 15% accuracy improvement |
| Bank-specific models | Fine-tuned Tesseract models per bank | 20% accuracy for top banks |
| Result caching | Cache OCR results by image hash | Skip re-processing duplicates |

---

## 4. Content Safety

### 4.1 Roast Content Guidelines

| Rule | Implementation |
|------|----------------|
| No personal insults | System prompt: "Прожаривай ПРИВЫЧКИ, не ЛИЧНОСТЬ" |
| No profanity | Keyword blocklist + LLM instruction |
| No discrimination | Protected categories in system prompt |
| No financial advice | Auto-disclaimer if keywords detected |
| No shaming poverty | System prompt: "Никогда не стыди за низкий доход" |
| No mental health triggers | Avoid: "бедный", "нищий", "безнадёжный" |

### 4.2 Content Moderation Pipeline

```
LLM Response → Keyword Filter → Sentiment Check → Output
                    ↓ (blocked)        ↓ (negative)
               Regenerate          Regenerate (softer prompt)
                    ↓ (2nd fail)       ↓ (2nd fail)
               Template fallback   Template fallback
```

### 4.3 Roast Templates (Fallback)

Pre-written templates for when LLM is unavailable or content fails moderation:

```
TEMPLATE_FOOD_DELIVERY:
    "Доставка еды: {amount}₽. Твой курьер уже поздравляет тебя с Днём рождения, потому что видит тебя чаще родственников 🎂"

TEMPLATE_TAXI:
    "Такси: {amount}₽. Тебе проще купить скутер — он окупится за {days} дней при таких тратах 🛵"

TEMPLATE_SUBSCRIPTIONS:
    "{count} подписок на {amount}₽/мес. Половину из них ты открывал последний раз при Ельцине 📺"

TEMPLATE_GENERIC:
    "За {period} потрачено {total}₽. Топ категория: {top_category} ({top_amount}₽). Может, пора задуматься? 🤔"
```

---

## 5. Error Handling Matrix

| Error Code | HTTP | Trigger | User Message | System Action |
|------------|:----:|---------|--------------|---------------|
| E_INPUT_001 | — | Invalid photo format | "Поддерживаю jpg и png" | Log, increment error counter |
| E_INPUT_002 | — | File > 10MB | "Файл слишком большой. Max 10MB" | Reject, log |
| E_INPUT_003 | — | Invalid CSV | "Не могу разобрать CSV" | Log, suggest alternatives |
| E_OCR_001 | — | OCR confidence < 50% | "Не могу разобрать фото" | Log, suggest retry |
| E_OCR_002 | — | Unknown bank format | "Банк не поддерживается" | Log bank pattern for future |
| E_LLM_001 | — | All providers failed | "AI на перекуре ☕" | Alert ops, use template |
| E_LLM_002 | — | Content filtered | (silent) | Retry with adjusted prompt |
| E_BIZ_001 | — | Free limit reached | Upsell message | Log conversion opportunity |
| E_BIZ_002 | — | < 5 transactions | "Маловато данных" | Prompt for more data |
| E_PAY_001 | — | Payment failed | "Оплата не прошла" | Retry 3x, then notify |
| E_PAY_002 | — | Webhook validation failed | (silent) | Log + alert security |
| E_SYS_001 | 500 | Database error | "Технические работы" | Alert ops, retry |
| E_SYS_002 | 500 | Redis unavailable | "Технические работы" | Alert ops, degrade gracefully |
| E_RATE_001 | 429 | Rate limit exceeded | "Слишком часто" | Block for cooldown period |

---

## 6. Security Hardening

### 6.1 Input Validation

| Input | Validation | Max Size | Sanitization |
|-------|-----------|:--------:|-------------|
| Photo | jpg/png MIME check | 10 MB | No executable content |
| CSV | .csv extension, text content | 5 MB | Encoding normalization |
| Text message | UTF-8 | 4,096 chars | Strip control characters |
| Callback data | Whitelist of known callbacks | 64 chars | Exact match only |
| Amount (manual) | Numeric, positive, < 10M | — | Parse as Decimal |

### 6.2 Secrets Management

| Secret | Storage | Rotation |
|--------|---------|----------|
| TELEGRAM_BOT_TOKEN | Environment variable | On compromise |
| OPENAI_API_KEY | Environment variable | Monthly |
| ANTHROPIC_API_KEY | Environment variable | Monthly |
| YANDEX_API_KEY | Environment variable | Monthly |
| DATABASE_URL | Environment variable | On compromise |
| REDIS_PASSWORD | Environment variable | On compromise |
| YOOKASSA_SECRET | Environment variable | On compromise |
| MINIO_ACCESS_KEY | Environment variable | Quarterly |

### 6.3 Dependency Security

- Dependabot enabled for all Python packages
- Monthly audit: `pip-audit`
- Pin all dependencies to exact versions in `requirements.txt`
- Docker images: use specific tags, not `latest`
