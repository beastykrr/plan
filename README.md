# Roadmap PL/SK — single-file dashboard

24-місячний (2026-09 → 2028-08) self-study roadmap для безкоштовного вступу до ВНЗ у Польщі або Словаччині через C1/B1-сертифікат мови.

## Як користуватись

1. Завантажити `roadmap.html` (raw): https://raw.githubusercontent.com/beastykrr/plan/main/roadmap.html
2. Зберегти локально (Save as → `roadmap.html`). Перед збереженням видалити старі версії (`roadmap (1).html`, `roadmap (2).html`...) з папки Downloads, інакше Chrome відкриє стару.
3. Відкрити подвійним кліком (`file://`). Працює offline.
4. **Експорт JSON раз на тиждень** (кнопка Налаштування → Експорт). Через 14 днів без експорту чекбокси задач блокуються — це навмисно (Poka-yoke), бо localStorage не довговічна.

## Поточна версія

`v3` (May 2026) — Poka-yoke та 18 технік навчання/дисципліни:
- BOTH-режим прибрано (PL ↔ SK перемикається, але не обидва primary).
- Hard-block чекбоксів якщо експорт прострочено &gt;14 днів.
- Файл перейменовано з `index.html` (запобігання плутанині downloads).
- Нова підвкладка «Тіло й розум → Методи & Дисципліна»: 18 технік з джерелами (Anki, active recall, interleaving, Feynman, shadowing, sentence mining, identity-based habits, habit stacking, implementation intentions, weekly review, sleep+memory, pre-mortem).

## Документи

- `PLAN_ANALYSIS.md` — обґрунтування архітектури, легальної бази, фітнес-плану, FactCheckR-каверзи.
- `roadmap.html` — сам дашборд.

## Вимоги

- Будь-який сучасний Chromium-based браузер (Chrome, Edge, Brave). Safari mobile може втрачати localStorage (ITP) — звідси 14-day backup hard-block.
- 0 серверних залежностей. Все в одному HTML-файлі (~2700 рядків, ~95KB).
