# ADR-005: Data Input Method — Screenshot/CSV First, Open API Later

**Статус:** Accepted
**Дата:** 2026-04-08
**Контекст:** Как получить данные о расходах пользователя без Open Banking API

## Решение

MVP: пользователь загружает скриншот банковской выписки или CSV файл. AI (vision model) извлекает транзакции. Open Banking API — Phase 2 (2027+).

## Контекст

| Метод | Friction | Accuracy | Tech complexity | Availability |
|-------|:--------:|:--------:|:---------------:|:------------:|
| **Screenshot + AI OCR** ✅ | Low | 85-90% | Medium | Now |
| **CSV upload** ✅ | Medium | 99% | Low | Now |
| Ручной ввод | High | 100% | Low | Now |
| Open Banking API | None | 100% | High | 2027+ |
| Screen scraping | None | 90-95% | Very High | Risky (ToS violation) |
| SMS parsing | Low | 80-85% | Medium | Declining (push replacing SMS) |

## Обоснование

1. **Open Banking в РФ задержан до 2027** — [ЦБ РФ](https://bosfera.ru/press-release/bank-rossii-perenes-sroki-obyazatelnogo-vnedreniya-open-api). Ждать нельзя
2. **Screenshot = lowest friction:** Пользователь уже в Telegram → делает скриншот из банк.приложения → кидает боту. 2 тапа
3. **Vision AI (GPT-4o, Claude) отлично парсят скриншоты** банковских выписок: суммы, категории, даты
4. **CSV — fallback для advanced users:** Сбер, Т-Банк, Альфа — все позволяют экспорт в CSV/Excel
5. **Дзен-мани прецедент:** синхронизация с 300+ банками через их API — но это собственные интеграции, не Open Banking — [App Store](https://apps.apple.com/ua/app/%D0%B4%D0%B7%D0%B5%D0%BD-%D0%BC%D0%B0%D0%BD%D0%B8-%D1%83%D1%87%D0%B5%D1%82-%D1%80%D0%B0%D1%81%D1%85%D0%BE%D0%B4%D0%BE%D0%B2/id905934786?l=ru)

## Roadmap

| Phase | Метод | Timeline |
|-------|-------|----------|
| MVP | Screenshot OCR + CSV upload | M1-M3 |
| V1.1 | + Ручной ввод через chat | M3-M4 |
| V1.2 | + Парсинг push-уведомлений (Android only) | M6 [H] |
| V2.0 | + Open Banking API (когда доступно) | 2027+ |

## Риски и митигация

| Риск | Митигация |
|------|----------|
| OCR ошибки в парсинге | Показывать распознанные данные → "Всё верно?" → user confirms |
| Разные форматы выписок | Train on top-5 банков: Сбер, Т-Банк, Альфа, ВТБ, Райффайзен |
| Privacy concerns (скриншоты) | Обработка in-memory, не хранить скриншоты, только extracted data |
| "Лень делать скриншоты каждый раз" | CSV для регулярного использования, screenshot — для quick roast |

## Последствия
- AI: Vision model (GPT-4o / Claude) для OCR банковских скриншотов
- Backend: parser для CSV форматов Сбер/Т-Банк/Альфа/ВТБ
- Storage: только extracted transactions, не raw images
- Privacy: end-to-end encryption, no-log policy для финансовых данных
