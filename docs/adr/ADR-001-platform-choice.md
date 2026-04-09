# ADR-001: Platform Choice — Telegram-first

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Выбор платформы для MVP AI-финансового коуча в РФ

## Решение

Telegram Bot как primary platform для MVP. Web app и native mobile app — Phase 2+.

## Контекст

Рассматривались 3 варианта:

| Вариант | Плюсы | Минусы |
|---------|-------|--------|
| **Native iOS + Android app** | Rich UI, push notifications, App Store presence | 3-6 мес разработки, высокий CAC ($5-15), модерация сторов |
| **Web app (PWA)** | Кроссплатформенность, быстрая итерация | Нет push (iOS), низкий retention, нужен SEO |
| **Telegram Bot** ✅ | 88M+ users в РФ, 0 барьер входа, push бесплатно, быстрый MVP | Ограниченный UI, сложная монетизация, зависимость от платформы |

## Обоснование

1. **88M+ пользователей Telegram в РФ** — не надо "приводить" пользователя в новое приложение
2. **CAC в 3-5x ниже** — forward бота vs App Store download
3. **MVP за 4-6 недель** — Bot API + LLM = минимальная инфраструктура
4. **Viral mechanics native:** пересылка сообщений, inline кнопки, групповые чаты
5. **Micro-trend #7 (Embedded Finance):** финансы внутри messenger = глобальный тренд — [FinMKT](https://www.finmkt.io/blog-posts/embedded-finance-trends-2025)

## Риски и митигация

| Риск | Митигация |
|------|----------|
| Telegram заблокируют в РФ (снова) | Web app как backup, данные в cloud |
| Ограниченный UI для графиков | Генерировать pie-charts как изображения (Matplotlib/Chart.js → PNG) |
| Монетизация через Telegram Payments | Альтернатива: ЮKassa deep link → внешняя оплата |
| Platform dependency | Планировать web/mobile Phase 2 с самого начала |

## Связанные микро-тренды
- [#7 Embedded Finance](../Micro_Trends_Research.md#7-embedded-finance--финансы-встроенные-в-нефинансовые-продукты)
- [#6 Russian PFM Gap](../Micro_Trends_Research.md#6-российский-pfm-рынок--gap-в-ai-сегменте)

## Последствия
- Backend: Python (python-telegram-bot + FastAPI)
- UI: Telegram Bot API (inline keyboards, images, mini apps)
- Payments: Telegram Payments API + ЮKassa fallback
- Analytics: собственная + Amplitude/Mixpanel through backend events
