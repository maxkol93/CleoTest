# GROWTH ENGINE: AI Финансовый Коуч → Россия
**Режим:** DEEP | **Дата:** 2026-04-08

---

## A. PRIMARY GROWTH LOOP

**Выбранный тип:** Product-Led Growth (PLG) с viral loop

**Почему:** Gen Z (18-30) в РФ — digital-native, 91% используют цифровые финансовые сервисы. Roast Mode = shareable content = organic growth. CAC через viral < paid channels.

### Механика Loop

```
Step 1: User видит roast-card друга в TikTok/VK/Telegram чате
    ↓
Step 2: Переходит в Telegram бот → /start → кидает скриншот выписки
    ↓
Step 3: Получает свой roast → 😂 "OMG это правда"
    ↓
Step 4: Шарит roast-card → друзья видят → Step 1 ↻
    ↓
Step 5: Data flywheel: больше users → больше roast patterns → смешнее roasts → больше shares
```

### Loop Metrics

| Метрика | Cleo (US) | Наш Target M6 | Target M12 | Confidence |
|---------|-----------|:--------------:|:----------:|:----------:|
| Bot starts / week | — | 800 | 3,000 | 0.65 |
| Upload → Roast completion | — | 60% | 75% | 0.70 |
| Roast → Share rate | — | 20% | 30% | 0.65 |
| K-factor (viral coefficient) | ~1.0 [H] | 0.5 | 1.2 | 0.60 |
| Share → New user conversion | — | 15% | 25% | 0.60 |

---

## B. TOP-3 ACQUISITION CHANNELS

| # | Канал | CAC | Conv. | Масштаб | Timing | Confidence |
|---|-------|:---:|:-----:|:-------:|--------|:----------:|
| 1 | **Viral Telegram + UGC** (roast cards shared in chats/groups) | 50-150₽ | 15-25% | High | 🟢 M1+ | 0.70 |
| 2 | **TikTok/VK Clips** (15-sec roast demos, creator collabs) | 200-400₽ | 3-5% | High | 🟡 M2-4 | 0.65 |
| 3 | **Telegram канал партнёрства** (финансовые/lifestyle каналы, 10K-100K subs) | 100-300₽ | 5-10% | Medium | 🟢 M1-3 | 0.70 |

**Channel-Unit Economics fit:**
- Channel 1 CAC (100₽) vs target CAC (250₽) → ✅ Below target
- Channel 2 CAC (300₽) vs target CAC (250₽) → ⚠️ At limit, but high scale
- Channel 3 CAC (200₽) vs target CAC (250₽) → ✅ Below target

---

## C. RETENTION PLAYBOOK

### Activation (первая сессия)

| Шаг | Действие | Метрика | Target |
|-----|----------|---------|:------:|
| 1 | /start → дерзкое приветствие | Bot start rate | 90% |
| 2 | Загрузить скриншот/CSV | Upload rate | 60% |
| 3 | 🔥 **Aha Moment:** получить первый roast | Roast completion | 55% |
| 4 | Поделиться roast-card | Share rate | 20% |

### Engagement (recurring)

| Механика | Описание | Частота |
|----------|----------|---------|
| Weekly Roast Report | Саркастичный summary за неделю | Weekly (понедельник) |
| Challenge of the Week | "Потрать на еду <5,000₽" → badge | Weekly |
| Streak System | "7 дней подряд следишь за бюджетом" | Daily |
| Subscription Hunter | "Нашёл подписку-паразит на 599₽" | On new data |
| Monthly Financial Grade | "Твоя оценка за март: C+ (рост с D!)" | Monthly |

### Churn Prevention

| Сигнал | Триггер | Действие |
|--------|---------|----------|
| 🟡 Risk | Нет активности 5 дней | "Эй, ты там не обанкротился? Кинь выписку 👀" |
| 🟡 Risk | Пропустил weekly challenge | "Challenge провален. Ну ничего, на этой неделе отыграешься" |
| 🔴 Churning | 14 дней inactive | "Без меня ты уже потратил ~X₽ на ерунду. Вернись!" |
| 🔴 Churning | Отмена подписки initiated | "Подожди! Вот что ты получил за этот месяц: сэкономил X₽" |
| ⚫ Churned | Подписка отменена | Exit survey (1 вопрос) + "Заморозь на 1 мес бесплатно" |

---

## D. MOATS (ранжированы по силе)

| # | Moat | Сила | Время | Описание |
|---|------|:----:|:-----:|----------|
| 1 | **Brand personality** | ●●●●● | 6-12 мес | "ФинРоуст" = категория. Первый AI-финансовый roaster в РФ. Скопировать personality сложно |
| 2 | **Data moat** | ●●●● | 12-18 мес | Транзакционные паттерны русскоязычных Gen Z → уникальные roast patterns и insights |
| 3 | **Community/UGC** | ●●●● | 6-12 мес | Библиотека roast-карточек, мемов, challenges. User-generated content = network effect |
| 4 | **Distribution (Telegram)** | ●●● | 3-6 мес | First-mover в Telegram PFM niche. Но low switching cost |
| 5 | **AI fine-tuning** | ●●● | 12+ мес | Fine-tuned модель на русском финансовом юморе. Конкурент не может скопировать без данных |

### Data Moat Detail

- **Какие данные:** Транзакционные паттерны 18-30 лет в РФ, реакции на roasts, engagement по категориям расходов
- **Уникальность:** Ни один банк не собирает данные о том, какой тип юмора мотивирует конкретного пользователя экономить
- **Feedback loop:** Больше users → лучше roasts → больше shares → больше users
- **Critical mass:** ~50K active users для статистически значимых паттернов

---

## E. SECOND-ORDER GROWTH EFFECTS

### Feedback Loops

```
🟢 Positive Loop: Roast virality
   More users → more roast shares → more UGC → more organic reach → more users
   Самоусиление начинается при K-factor > 0.7

🟢 Positive Loop: Data quality
   More transactions → better category detection → funnier roasts → more engagement → more transactions
   Самоусиление начинается при 50K+ transactions processed

🔴 Negative Loop: Content fatigue
   Same roast patterns → boring → lower share rate → lower K-factor → growth stalls
   Доминирует при DAU/MAU < 15%

🔴 Negative Loop: Reputation risk
   Offensive roast → bad review → PR crisis → trust loss
   Доминирует при NPS < 0

⚖️ Tipping Point: positive > negative при WAR (Weekly Active Roasters) > 5,000
```

### Competitive Reactions

| Наше действие | Вероятная реакция | Наш counter |
|---------------|-------------------|-------------|
| Launch Telegram roast bot | Т-Банк: "мы тоже можем добавить юмор в Олега" | Углубить community: challenges, leaderboards, UGC. Банк не может создать meme culture |
| Viral TikTok content | Дзен-мани: "добавим AI chat" | Ускорить iteration. У нас AI-first architecture, у них — legacy |
| 100K users milestone | Сбер: "запустим аналог" | К этому моменту у нас brand + data moat. Сбер = "для взрослых" |

---

## F. CONTENT & GROWTH MARKETING

### Content Strategy

| Формат | Канал | Частота | Цель |
|--------|-------|---------|------|
| Roast demos (15 сек) | TikTok/VK Clips | 3-5/неделю | Awareness + viral |
| "Самые тупые траты недели" | Telegram канал | 1/неделю | Engagement + shares |
| Financial tips с юмором | VK/Instagram | 2-3/неделю | SEO + trust |
| Creator collabs | TikTok | 2/месяц | Reach new audiences |
| Meme templates | Telegram/VK | По ситуации | UGC generation |

### SEO / ASO (Phase 2 — web/mobile app)

| Ключевые слова | Объём [H] | Конкуренция |
|----------------|:---------:|:-----------:|
| "учёт расходов приложение" | High | High (Дзен-мани, CoinKeeper) |
| "прожарка расходов" | Low→Medium | **None** (мы создаём категорию) |
| "AI финансовый советник" | Medium | Low |
| "куда уходят деньги" | High | Medium |
| "бюджет для молодёжи" | Medium | Low |

---

## Confidence Summary

| Блок | Avg Confidence | Min |
|------|:--------------:|:---:|
| Growth Loop | 0.65 | 0.60 |
| Channels | 0.68 | 0.65 |
| Retention | 0.70 | 0.65 |
| Moats | 0.72 | 0.65 |
| Second-Order | 0.65 | 0.60 |
| **ИТОГО** | **0.68** | **0.60** |

**Note:** Growth metrics имеют самый низкий confidence — это гипотезы, которые нужно валидировать на MVP. Viral K-factor и конверсии — первые метрики для A/B тестирования.
