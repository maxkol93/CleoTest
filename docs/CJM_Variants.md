# Customer Journey Map: AI Финансовый Коуч для РФ (Клон Cleo)
**Дата:** 2026-04-08 | **Режим:** DEEP | **География:** Россия

---

## Контекст

На основе данных из M1 (Fact Sheet), M2 (Product & Customers) и [Micro-Trends Research](./Micro_Trends_Research.md) предлагаются 3 варианта CJM для запуска AI-финансового коуча в России.

Каждый вариант различается по:
- **Aha Moment** — когда пользователь понимает ценность
- **Entry Hook** — как формулируем предложение
- **Monetization** — когда и как paywall

---

## Variant Table (сравнение)

| | **A: "Прожарка"** | **B: "Умный копилка"** | **C: "Telegram-first"** |
|---|---|---|---|
| **Aha Moment** | Первый roast расходов — вирусный момент "OMG это правда про мои Яндекс.Лавки" | Автоматический расчёт: "ты мог бы сэкономить 12,400₽/мес" | AI-бот в Telegram анализирует скриншот выписки за 30 секунд |
| **Entry Hook** | Emotional Job: "Хватит прятаться от своих трат — давай посмеёмся" | Functional Job: "Узнай куда уходят деньги и начни копить автоматически" | Social Job: "Финансовый ассистент пр��мо в Telegram — не надо скачивать ещё одно приложение" |
| **Onboarding** | 3 вопроса: возраст, цель, "готов к правде?" → загрузить выписку | 5 вопросов: доход, цели, подписки → подключить банк/CSV | 0 вопросов: просто скинь скриншот или CSV выписки |
| **Core Loop** | Еженедельный "roast" + weekly challenge (потрать на 10% меньше) | Daily: сколько можешь потратить сегодня. Weekly: прогресс к цели | Push-уведомления в Telegram: "Ты потратил 3,200₽ на кофе. Серьёзно?" |
| **Paywall** | После 3-го roast (эмоциональный hook): "Хочешь детальный план экономии?" | После показа "могу сэкономить X₽" (proof of value): "Включить автосбережения?" | После первого бесплатного анализа: "Хочешь ежедневный мониторинг?" |
| **Invite** | "Прожарь друга" — share roast card в соцсети | "Пригласи друга — получи +1 мес бесплатно" | Forward бот в групповой чат |
| **Best for Segment** | Broke but Self-Aware (18-25) | Aspiring Saver (24-32) | Все сегменты (lowest friction) |
| **Hypothesis** | H: Вирусность roast контента на русском = organic growth ≥ Cleo US | H: Конвертация "экономия X₽" > конвертация "roast" для RU market | H: Telegram-first снижает CAC в 3-5x vs native app |
| **Risk** | Русский юмор ≠ английский, может не зайти. "Прожарка" может обид��ть | Utility-only = скучно, нет вирусности. Конкурирует с банковскими PFM | Telegram-бот ограничен в UI. Монетизация сложнее |
| **Micro-trend leverage** | [#2 Gamification](./Micro_Trends_Research.md#2-gamification-of-financial-literacy), [#5 ФГ парадокс](./Micro_Trends_Research.md#5-финансовая-грамотность-молодёжи-рф--парадокс) | [#1 AI Personalization](./Micro_Trends_Research.md#1-ai-powered-hyper-personalization-в-pfm), [#4 Open Banking](./Micro_Trends_Research.md#4-open-banking-в-россии--задержка-и-возможности) | [#7 Embedded Finance](./Micro_Trends_Research.md#7-embedded-finance--финансы-встроенные-в-нефинансовые-продукты), [#6 PFM Gap](./Micro_Trends_Research.md#6-российский-pfm-рынок--gap-в-ai-сегменте) |

---

## Scoring (1-5 по каждому критерию)

| Критерий | **A: Прожарка** | **B: Умный копилка** | **C: Telegram-first** |
|----------|:---:|:---:|:---:|
| Вирусный потенциал | ⭐⭐⭐⭐⭐ (5) | ⭐⭐ (2) | ⭐⭐⭐ (3) |
| Монетизация | ⭐⭐⭐ (3) | ⭐⭐⭐⭐ (4) | ⭐⭐ (2) |
| Техническая сложность MVP | ⭐⭐⭐ (3) | ⭐⭐ (2) | ⭐⭐⭐⭐ (4) — проще всего |
| Product-Market Fit для РФ | ⭐⭐⭐⭐ (4) | ⭐⭐⭐ (3) | ⭐⭐⭐⭐ (4) |
| Retention потенциал | ⭐⭐⭐ (3) | ⭐⭐⭐⭐⭐ (5) | ⭐⭐⭐ (3) |
| Defensibility (moat) | ⭐⭐⭐ (3) | ⭐⭐⭐⭐ (4) | ⭐⭐ (2) |
| **ИТОГО** | **21/30** | **20/30** | **18/30** |

---

## Рекомендация: Гибрид A+C → "Прожарка в Telegram"

**Оптимальный вариант объединяет:**
- 🔥 **Aha Moment из A** — Roast Mode (вирусность + engagement)
- 📱 **Платформу из C** — Telegram-first (lowest friction для РФ, 88M+ users)
- 💰 **Monetization из B** — Proof of value ("сэкономишь X₽") → подписка

### Итоговый CJM: "ФинРоуст" (рабочее название)

---

## Stage 1: AWARENESS (Осведомлённость)

**Канал:** TikTok/Reels/VK Клипы → Telegram

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| Видео "Прожарь свои траты" в TikTok/VK | Смотрит 15-сек ролик с примером roast | 😂 Смех + узнавание | Views, CTR |
| Ссылка на Telegram-бот в bio | Кликает → переходит в Telegram | 🤔 Любопытство | Click-through rate |
| **Micro-trend:** [#2 Gamification](./Micro_Trends_Research.md#2-gamification-of-financial-literacy) — roast как entertainment + финграмотность | | | |

**Барьеры:** "Ещё один бот", "а вдруг мошенники"
**Снятие:** Прозрачность (никаких данных банка на старте), юмор снижает guard

---

## Stage 2: ACTIVATION (Первый опыт)

**Канал:** Telegram Bot

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| Приветствие бота с характером | Нажимает /start, видит дерзкое приветствие | 😏 Интрига | Bot starts |
| "Кинь мне скриншот выписки или CSV" | Загружает скриншот/фото выписки из банк.приложения | 🤷 "Ну ок, посмотрим" | Upload rate |
| 🔥 **AHA MOMENT:** Первая "прожарка" | Получает саркастичный анализ: "4,800₽ на Яндекс.Лавку за неделю? Ты что, шеф-повар?" | 😂🤯 Wow + shame + мотивация | Share rate (KEY) |
| "Поделись прожаркой с другом" | Пересылает roast-карточку друзьям | 🎉 Social sharing | K-factor |
| **Micro-trend:** [#3 Gen Z Trust AI](./Micro_Trends_Research.md#3-gen-z-trust-in-ai-for-financial-advice) — "задать вопрос, который стыдно задать людям" | | | |
| **Micro-trend:** [#7 Embedded Finance](./Micro_Trends_Research.md#7-embedded-finance--финансы-встроенные-в-нефинансовые-продукты) — финансы внутри Telegram | | | |

**Барьеры:** "Я не хочу показывать свои расходы"
**Снятие:** "Всё обрабатывается AI, ни один человек не видит" + end-to-end encryption

---

## Stage 3: ENGAGEMENT (Вовлечение)

**Канал:** Telegram Bot + Push-уведомления

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| Еженедельный "Roast Report" | Получает саркастичный отчёт за неделю | 😬😂 Anticipation | Open rate |
| Weekly Challenge | "Потрать на кофе на 20% меньше — получи badge" | 🎮 Азарт | Challenge completion |
| "Подписки-паразиты" detector | AI находит забытые подписки: "Ты платишь 599₽/мес за Кинопоиск и ни разу не зашёл" | 😡→💡 Anger → action | Subscription cancellation rate |
| Категоризация расходов + пай-чарт | Интерактивная визуализация: топ-5 категорий | 📊 Понимани�� | Engagement time |
| **Micro-trend:** [#1 AI Personalization](./Micro_Trends_Research.md#1-ai-powered-hyper-personalization-в-pfm) — коуч запоминает паттерны и адаптируется | | | |
| **Micro-trend:** [#5 ФГ парадокс](./Micro_Trends_Research.md#5-финансовая-грамотность-молодёжи-рф--парадокс) — edutainment через юмор | | | |

**Барьеры:** "Надоело, хватит стыдить"
**Снятие:** Hype Mode (переключение на позитив), прогресс-бар к целям

---

## Stage 4: MONETIZATION (Монетизация)

**Канал:** Telegram Bot → In-app payments / ЮKassa

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| "Я нашёл как тебе сэкономить 8,700₽/мес" (proof of value) | Видит конкретную сумму экономии | 💰 Жадность + любопытство | CTA click rate |
| Paywall: "Хочешь детальный план? 399₽/мес" | Оценивает value vs cost | 🤔 Калькуляция | Conversion rate |
| **Pricing (адаптирован для РФ):** | | | |
| Free: 1 roast/неделю + базовая категоризация | Пользуется бесплатно | 😊 Достаточно | Free tier usage |
| Pro: 399₽/мес — безлимит roasts, auto-savings plan, подписки-детектор, weekly challenges | Подписывается | 💪 Investment in self | MRR |
| Premium: 799₽/мес — AI-коуч с памятью, голосовые сообщения, кредитный скоринг | Апгрейдится | 🚀 Commitment | ARPU |
| **Micro-trend:** [#6 PFM Gap](./Micro_Trends_Research.md#6-российский-pfm-рынок--gap-в-ai-сегменте) — нет прямых конкурентов с таким positioning | | | |

**Барь��ры:** "399₽/мес — дорого для бота"
**Снятие:** "Ты тратишь 8,700₽ впустую. 399₽ — это 4.6% от экономии"

---

## Stage 5: RETENTION (Удержание)

**Канал:** Telegram + Email

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| Monthly "Финансовый Roast Report" | Получает развёрнутый отчёт с прогрессом | 📈 Гордость | Monthly active rate |
| Streak system | "7 дней подряд следишь за бюджетом" → badge | 🎮 Привычка | D7/D30 retention |
| Personalized tips | AI учится на паттернах: "Ты тратишь больше по пятницам — вот почему" | 💡 Инсайт | Tip engagement |
| Seasonal challenges | "Чёрная пятница survival guide", "Новогодний бюджет" | 🎯 Актуальность | Challenge participation |
| **Micro-trend:** [#8 Voice-First](./Micro_Trends_Research.md#8-voice-first-ai-financial-assistants) ��� Phase 2: голосовые сообщения от AI-коуча | | | |

**Churn Prevention:**

| Сигнал | Триггер | Действие |
|--------|---------|----------|
| 🟡 Risk | Нет активности 5 дней | "Эй, ты там не обанкротился? Кинь выписку 👀" |
| 🔴 Churning | 14 дней inactive | "Без меня ты уже потратил ~X₽ на ерунду. Вернись!" |
| ⚫ Churned | Отменил подписку | Exit survey + "Замори 1 месяц бесплатно" |

---

## Stage 6: ADVOCACY (Адвокатство)

**Канал:** Социальные сети + Telegram

| Touchpoint | Действие пользователя | Эмоция | Метрика |
|------------|----------------------|--------|---------|
| "Прожарь друга" share card | Пересылает roast-card в чаты/stories | 😂 Fun + social capital | Referral rate |
| Referral program | "Приведи друга — оба получаете Pro на месяц бесплатно" | 🤝 Win-win | K-factor |
| UGC: скриншоты roasts | Постят в TikTok/VK самые смешные roasts | 📱 Viral content | Organic reach |
| Community challenge | "Кто сэкономит больше за месяц?" — leaderboard | 🏆 Competition | Community engagement |

---

## Детальное сравнение 3 вариантов по этапам CJM

### Awareness

| Аспект | A: Прожарка | B: Умный копилка | C: Telegram-first |
|--------|------------|-----------------|-------------------|
| Главный канал | TikTok/VK: viral roast demos | Instagram/VK: "сэкономь X₽" calculator | Telegram каналы + word-of-mouth |
| Hook message | "Узнай правду о своих тратах" | "Узнай куда уходят деньги" | "Кинь выписку — получи анализ за 30 сек" |
| CAC estimate | $1-3 (viral) | $5-10 (paid social) | $0.5-2 (organic in Telegram) |
| Вирусный K-factor | 1.5+ (shareable content) | 0.3 (utility, not shareable) | 0.8 (bot forwarding) |

### Activation

| Аспект | A: Прожарка | B: Умный копилка | C: Telegram-first |
|--------|------------|-----------------|-------------------|
| Steps to Aha | 3 (start → upload → roast) | 5 (start → questions → connect → wait → insight) | 2 (start → upload) |
| Time to value | 2 min | 5-10 min | 30 sec |
| Drop-off risk | Medium (upload step) | High (5 questions + bank connect) | Low (screenshot = easy) |

### Monetization

| Аспект | A: Прожарка | B: Умный копилка | C: Telegram-first |
|--------|------------|-----------------|-------------------|
| Paywall trigger | After 3rd roast (emotional) | After savings calculation (rational) | After first analysis (curiosity) |
| Conversion est. | 5-8% (emotional impulse) | 8-12% (rational value proof) | 3-5% (low commitment) |
| ARPU potential | Medium (entertainment) | High (utility = willingness to pay) | Low (bot perception = cheap) |

### Retention

| Аспект | A: Прожарка | B: Умный копилка | C: Telegram-first |
|--------|------------|-----------------|-------------------|
| D30 retention est. | 25-35% (entertainment + habit) | 20-30% (utility but boring) | 15-25% (low switching cost) |
| Moat | Brand personality | Data + savings automation | Distribution (already in Telegram) |
| Churn risk | "Novelty wears off" | "Not enough new features" | "Easy to delete, easy to forget" |

---

## Рекомендуемый гибрид: A+C

**Почему этот вариант оптимален:**

1. **Telegram-first** решает главную проблему acquisition: 88M+ пользователей в РФ, нулевой барьер входа, не надо скачивать app
2. **Roast Mode** решает главную проблему retention: entertainment > utility для Gen Z
3. **Proof-of-value paywall** (из B) решает monetization: "ты сэкономишь X₽" > "купи подписку"
4. **Micro-trend alignment:** покрывает 7 из 10 выявленных трендов
5. **Technical simplicity:** Telegram Bot + LLM API = MVP за 4-6 недель

**ADR:** Решение зафиксировано в [ADR-001: Platform Choice](./adr/ADR-001-platform-choice.md)

---

## Metrics Framework (North Star + Supporting)

| Тип | Метрика | Target M3 | Target M12 |
|-----|---------|:---------:|:----------:|
| ⭐ North Star | Weekly Active Roasters (WAR) | 5,000 | 50,000 |
| Acquisition | Bot starts / week | 2,000 | 15,000 |
| Activation | Upload → Roast completion rate | 60% | 75% |
| Engagement | Roasts shared / week | 500 | 10,000 |
| Revenue | MRR | 200K₽ | 5M₽ |
| Retention | D30 retention | 25% | 35% |
| Referral | K-factor (viral coefficient) | 0.5 | 1.2 |
