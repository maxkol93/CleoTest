# ADR-006: LLM Provider — Multi-provider с приоритетом на доступность в РФ

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Выбор LLM для AI-финансового коуча в РФ

## Решение

Primary: OpenAI API (GPT-4o) через proxy/VPN. Fallback: Anthropic Claude API. Local alternative: YandexGPT / GigaChat для российского рынка. Multi-provider architecture с самого начала.

## Контекст

| Provider | Quality | Русский язык | Доступность в РФ | Стоимость |
|----------|:-------:|:------------:|:-----------------:|:---------:|
| OpenAI GPT-4o | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⚠️ Через proxy | $2.5-10/1M tokens |
| Anthropic Claude | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⚠️ Через proxy | $3-15/1M tokens |
| YandexGPT | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅ Native | ~300₽/1M tokens |
| GigaChat (Сбер) | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅ Native | API free tier available |
| Open source (Llama 3, Mistral) | ⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ Self-hosted | Infra cost |

## Обоснование

1. **Quality first:** GPT-4o/Claude = лучшее качество для personality-driven chatbot
2. **Sanctions risk:** OpenAI/Anthropic могут ограничить доступ из РФ → multi-provider обязателен
3. **YandexGPT как fallback:** Нативный русский, доступен без ограничений, но качество personality ниже
4. **Micro-trend #8:** [Voice-First](../Micro_Trends_Research.md#8-voice-first-ai-financial-assistants) — TTS от YandexSpeechKit = лучший русский voice
5. **Cost optimization:** Roast generation = short prompts, low token count → cost-effective

## Architecture

```
User request → Router → 
  ├── GPT-4o (primary, best personality)
  ├── Claude (fallback #1, best reasoning)
  ├── YandexGPT (fallback #2, guaranteed RU access)
  └── GigaChat (fallback #3, free tier)
```

## Риски и митигация

| Риск | Митигация |
|------|----------|
| OpenAI blocked in Russia | Auto-failover to YandexGPT within seconds |
| Quality degradation on fallback | Fine-tune system prompts per provider |
| API costs at scale | Cache common roast patterns, use cheaper models for classification |
| Data residency requirements | Process financial data locally, send only anonymized summaries to LLM |

## Последствия
- Architecture: LLM abstraction layer (LiteLLM or custom router)
- Prompts: provider-specific system prompts (GPT vs Claude vs Yandex)
- Monitoring: quality scoring per provider, automatic switching
- Data: anonymize financial data before sending to external LLM
