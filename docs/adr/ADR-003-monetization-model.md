# ADR-003: Monetization Model — Freemium с Proof-of-Value Paywall

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Выбор модели монетизации для российского рынка

## Решение

Freemium: бесплатный базовый roast + платная подписка после демонстрации конкретной суммы экономии.

## Контекст

| Модель | ARPU est. | Conversion | Риск |
|--------|:---------:|:----------:|------|
| **Freemium + proof-of-value** ✅ | 399-799₽/мес | 5-8% | Нужно время до proof |
| Paywall-first (roast за деньги) | 299₽/мес | 1-3% | Убивает вирусность |
| Ads-supported | 50-100₽/мес RPU | N/A | Low CPM в РФ, раздражает |
| Transaction fee | Variable | N/A | Требует banking license |
| One-time purchase | 1,499₽ | 2-4% | Нет recurring revenue |

## Обоснование

1. **Free tier = viral engine:** Бесплатный roast → шеринг → organic growth
2. **Proof-of-value > emotional impulse:** "Ты можешь сэкономить 8,700₽/мес" → "399₽ — это 4.6% от экономии" = rational conversion
3. **Cleo pricing adapted:** $5.99/мес → 399₽/мес (паритет покупательной способности, $5.99 × ~67₽ ≈ 400₽, но российские реалии: снизить до 399₽)
4. **Russian subscription habits:** Молодёжь привыкла к подпискам (Яндекс.Плюс 299₽, Spotify 169₽), но 399₽ — верхний порог для "бота"
5. **FTC lesson:** No deceptive promises. Показываем реальную экономию, не "до X₽"

## Тарифная сетка

| Тариф | Цена | Включено |
|-------|------|----------|
| Free | 0₽ | 1 roast/неделю, базовая категоризация, top-3 расходов |
| Pro | 399₽/мес (3,499₽/год) | Безлимит roasts, детальный план экономии, подписки-детектор, weekly challenges, pie-charts |
| Premium | 799₽/мес (6,999₽/год) | Всё из Pro + AI-коуч с памятью, персонализированные советы, голосовые roasts, приоритетная поддержка |

## Риски и митигация

| Риск | Митигация |
|------|----------|
| 399₽ дорого для целевой аудитории (18-25 broke) | Годовая подписка со скидкой 27%, студенческий тариф [H] |
| Low conversion на Free → Pro | A/B test paywall triggers: 3-й roast vs proof-of-value vs time-based |
| Churn после первого месяца | Monthly report с "сколько ты сэкономил с нами" |
| ЮKassa комиссия 3.5% | Учтена в unit economics |

## Последствия
- Payments: ЮKassa API (карты, SBP, Telegram Payments)
- Billing: рекуррентные платежи с easy cancel (1 кнопка)
- Metrics: track conversion по каждому paywall trigger variant
- Legal: оферта + политика возврата (7 дней) с первого дня
