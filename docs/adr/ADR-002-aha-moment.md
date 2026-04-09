# ADR-002: Aha Moment — Roast Mode как Core Experience

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Выбор ключевого Aha Moment для первой сессии пользователя

## Решение

Первый "roast" (саркастичный анализ расходов) = Aha Moment. Пользователь загружает выписку → получает язвительный, но точный комментарий за <2 минуты.

## Контекст

| Вариант Aha Moment | Конверсия est. | Вирусность | Время до Aha |
|--------------------|:--------------:|:----------:|:------------:|
| **Roast расходов** ✅ | 5-8% | ⭐⭐⭐⭐⭐ | 2 мин |
| "Ты можешь сэкономить X₽" | 8-12% | ⭐⭐ | 5-10 мин |
| Pie-chart категорий | 3-5% | ⭐ | 3 мин |
| Подписки-паразиты | 6-9% | ⭐⭐⭐ | 5 мин |

## Обоснование

1. **Вирусность > конверсия на старте:** Для bootstrapped стартапа organic growth критичнее, чем высокая конверсия. Roast-карточки = UGC контент для TikTok/VK
2. **Cleo доказала модель:** Roast Mode = главный differentiator Cleo, driver of 6M+ users — [meetcleo.com](https://web.meetcleo.com/blog/the-money-app-that-roasts-you)
3. **Gen Z engagement:** Gamification through personality = proven pattern — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S2214635026000420)
4. **Российская специфика:** Культура "прожарки" (roast battles, Stand-Up Club, КВН) — native format
5. **Low technical bar:** LLM + system prompt = MVP in days, не месяцы

## Риски и митигация

| Риск | Митигация |
|------|----------|
| Русский юмор ≠ английский | A/B тестирование тона: "дерзкий друг" vs "строгая тётя из банка" vs "мем-лорд" |
| Roast может обидеть | Hype Mode toggle: переключение на позитивную мотивацию |
| Novelty wears off | Постепенно добавлять utility: savings plan, goals, challenges |
| FTC прецедент (Cleo $17M) | С первого дня: прозрачные terms, easy cancel, no deceptive promises |

## Последствия
- LLM: system prompt с "саркастичный финансовый советник" persona
- Content: библиотека roast-шаблонов для разных категорий расходов
- Share: генерация shareable card (image) для каждого roast
- Tone settings: user can choose roast intensity (мягкий / средний / без пощады)
