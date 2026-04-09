# ADR-004: AI Personality — Адаптация "Roast" персонажа для РФ

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Выбор тональности AI-персонажа для российской аудитории

## Решение

"Дерзкий друг" — саркастичный, но не злой. Комбинация мем-культуры и реальной пользы. Пользователь может переключать тон.

## Контекст

| Persona | Примеры | Плюсы | Минусы |
|---------|---------|-------|--------|
| **"Дерзкий друг"** ✅ | "Ну ты даёшь — 5К на Яндекс.Еду за неделю 🤦" | Natural для Gen Z РФ, мем-friendly | Может надоесть |
| "Строгий бухгалтер" | "Ваши расходы на питание превышают рекомендуемый бюджет на 47%" | Серьёзный, доверие | Скучно, как банковское приложение |
| "Мем-лорд" | "Твой бюджет: *I'm in danger* 🔥" | Максимальная вирусность | Не воспринимают серьёзно |
| "Заботливая подруга" | "Слушай, я за тебя переживаю — давай посмотрим куда уходят деньги?" | Эмпатия, retention | Низкая вирусность |

## Обоснование

1. **Культурный контекст РФ:** Stand-Up Club, КВН, мем-культура = "прожарка" — родной формат
2. **Balance humor + utility:** Cleo доказала: personality > pure utility для Gen Z — [neoreach.com](https://neoreach.com/campaign-teardown-cleo/)
3. **Tone switching** решает problem of "novelty wearing off": 
   - 🔥 Roast Mode (дерзкий)
   - 🎉 Hype Mode (поддержка)
   - 📊 Serious Mode (просто факты)
4. **Russian meme adaptation:** Не переводить Cleo, а создать нативного русского персонажа
5. **Micro-trend #5:** [ФГ парадокс](../Micro_Trends_Research.md#5-финансовая-грамотность-молодёжи-рф--парадокс) — молодёжь "грамотна" формально, но не на практике. Edutainment = решение

## System Prompt Architecture

```
Base persona: "Ты — саркастичный финансовый друг. Ты искренне хочешь помочь, 
но делаешь это через юмор и прямоту. Ты никогда не врёшь и не приукрашиваешь. 
Ты используешь российские мемы и сленг Gen Z."

Tone modifiers:
- roast_level: 1-5 (1 = мягкий, 5 = без пощады)
- use_memes: true/false
- формальность: low (default) / medium / high
```

## Риски и митигация

| Риск | Митигация |
|------|----------|
| Обидеть пользователя | Tone slider + "Слишком жёстко? Переключи на Hype Mode" |
| Юмор не зайдёт | A/B test 3 persona variants, iterate based on engagement |
| Sensitive topics (долги, бедность) | AI guidelines: никогда не шутить над бедностью, только над конкретными тратами |
| Cultural missteps | Content review board + user feedback loop |

## Последствия
- LLM: system prompt с persona + tone modifiers
- Content: библиотека roast-шаблонов по категориям (еда, подписки, транспорт)
- Settings: UI для выбора тона (в Telegram: inline keyboard)
- Safety: content filter для sensitive financial situations
