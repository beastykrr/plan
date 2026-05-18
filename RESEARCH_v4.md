# Research log — v4 (post-merge of PR #3)

Мета: знайти **робочі** доповнення для дашборду й плану. Нічого «красивого». Тільки методики з джерелом + UX-патерни, що повторюються в успішних публічних дашбордах.

Всі джерела перевірені web search'ем у цій сесії (2026-05). У JSON-форматі цитати в посиланнях.

---

## 1. Нові методи (поверх 18 наявних)

### 1.1 FSRS algorithm — заміна SM-2 в Anki

**Чому додати:** новіший алгоритм планування Anki, з 2022 (open-spaced-repetition), доступний в Anki 23.10+ як опція; з 24.11 розглядається як новий default. На бенчмарку розробника (open-spaced-repetition/srs-benchmark, GitHub) FSRS з default-параметрами кращий за SM-2 у **91.9%** випадків; FSRS-5 з оптимізованими параметрами кращий у **99.0%** випадків.

**Конкретно для дашборду:** в картці «Spaced Repetition» додати warning-блок з покроковою інструкцією як увімкнути FSRS (Deck Options → Advanced → FSRS toggle), щоденну optimize-команду раз на місяць.

**Sources:**
- Anki FAQ (ankiweb.net) — «What spaced repetition algorithm does Anki use?»
- open-spaced-repetition/fsrs4anki repo, 3940+ зірок, 177 релізів, last v6.1.3
- L-M-Sherlock benchmark plot — superiority-9999.png on SRS-benchmark
- ankitects/anki#3616 — public issue obговорення "Make FSRS the default?"

### 1.2 Method of Loci (memory palace)

**Чому додати:** mета-аналіз 2025 (PubMed 40457944) показав **d = 0.88** (large effect) для immediate serial recall у дорослих. Активує гіпокамп, парагіпокамп, retrosplenial cortex (нейроімаджинг confirmed).

**Конкретно:** виключно для **списків** (не для concepts):
- польські іменники по родах (m/f/n) — палац з 3 кімнат
- дати з історії — кожна кімната = століття
- математичні формули (тригонометрія) — 10 локусів = 10 формул
- запам'ятовування Іспиту C1: 4 секції (listening/reading/grammar/writing) → 4 локуси у звичній кімнаті

**Sources:**
- Twomey & Kroneisen *Quarterly Journal of Experimental Psychology* 2021 (meta-analysis)
- Müller et al. *Science Advances* 2021 (memory athletes, durable memories, neural coding)
- PubMed PMID 40457944 (2025 systematic review + meta-analysis)

### 1.3 Cognitive Load Theory (Sweller)

**Чому додати:** одна з найвпливовіших теорій навчання, але школярі майже ніколи не знають практичних наслідків. Working memory обмежена (~4±1 elements), long-term — безмежна. Завдання вчителя/самонавчання — мінімізувати extraneous load, максимізувати germane load.

**Ключові ефекти:**
- **Worked example effect** — на ранньому етапі вивчення math/Python кращe **читати** розв'язані приклади ніж намагатися розв'язати самому. Тільки після ~5-10 прикладів — починати розв'язувати.
- **Split-attention effect** — текст і малюнок (формула + графік) **повинні бути на одній сторінці**, не «текст вгорі, малюнок знизу окремо».
- **Redundancy effect** — НЕ читай конспект, поки слухаєш лекцію з тими ж словами. Або одне або інше.
- **Expertise reversal** — для **експерта** worked examples гірші, кращe власне розв'язування. Перехід — після ~10 успішних рішень самостійно.

**Конкретно для НМТ math:** перші 10 задач кожної нової теми — читай **готові розв'язки** з поясненням. Потім перехід на cold-solve.

**Sources:**
- Sweller *Cognitive Science* 1988; *Cognitive Load Theory* book 2011
- Greg Ashman webinar — Education Hub NZ (2024-09)
- NSW Department of Education CLT classroom practice guide

### 1.4 Deep Work (Cal Newport)

**Чому додати:** доповнення до Pomodoro/52-17. Newport визначає 4 рівні часу:
- **Deep work** — без перерв, без notifications, на cognitively demanding task. Студент може витримати **2-4 години deep work на день** (це жорсткий ліміт; expert performers не сильно більше).
- **Shallow work** — email, легкі задачі.
- **Attention residue (Leroy 2009 OBHDP):** коли переключаєшся з одного завдання на інше, частина уваги залишається на попередньому → продуктивність падає.

**Конкретно:**
- Telephone у іншій кімнаті (не silent — фізично відсутній)
- Окремий «робочий» профіль браузера без соц.мереж
- Block 90-120 хв уранці на найважче (math/мова)
- Не «10 хв проверю TikTok → 1 година math → 5 хв TikTok» — це 0% deep work через attention residue

**Sources:**
- Newport *Deep Work* (2016)
- Leroy *Organizational Behavior and Human Decision Processes* 2009, vol. 109, pp. 168-181 (attention residue)

### 1.5 Rule 52/17 (DeskTime 2014)

**Чому додати:** альтернатива до класичного Pomodoro 25/5. DeskTime проаналізувала топ-10% продуктивних користувачів і виявила, що вони працюють у блоках **52 хв роботи + 17 хв перерви**. Збігається з тим, що знаходять у літературі про deep work + default mode network (15-20 хв reset).

**Конкретно:** для важких блоків (math problem set, написання C1 essay) — 52/17. Для легких (Anki review, vocab drill) — Pomodoro 25/5. Для дуже важких (mock-exam imitation) — 90 хв non-stop, потім 30 хв walk.

**Sources:**
- The Muse 2014 — оригінальна стаття за результатами DeskTime
- DeskTime blog 2024-10-03 — updated verification
- Caveat: не RCT, просто observational на DeskTime data

### 1.6 Chronotype awareness

**Чому додати:** підлітки (15-16) мають біологічну затримку фази сну (~2-3 години vs дорослих). Пік алертності — не «8 ранку» як рекомендують loose advice books, а реально **11:00-14:00** або **16:00-19:00** (залежно від chronotype).

**Дані з research:**
- Jankowski, Díaz-Morales, Vollmer *Journal of Intelligence* 2023 — synchrony effect: учні показують кращі результати у тестах **кристалізованого** інтелекту (factual knowledge, language) у свій оптимальний час. Для flux intelligence (problem-solving) ефект слабший.
- Springer 2025 (Current Sleep Medicine Reports) — циркадне misalignment у підлітків знижує когнітивну продуктивність + школу start times слід зсунути пізніше.

**Конкретно для дашборду:**
- Налаштування → chronotype (Morning / Neutral / Evening) — single-select
- На сторінці «Сьогодні» — badge «Зараз твій peak slot» якщо поточний час потрапляє в твій оптимальний slot
- Рекомендація: math/мову вчити в peak slot, Anki review — у будь-який час

**Сторінки:**
- За замовчуванням підліток ≈ evening type (delayed sleep phase) → peak 11-14 + 16-19
- Якщо ти ранкова людина (рідко в 15) → 8-11 + 14-16

**Sources:**
- Jankowski et al. *Journal of Intelligence* 11(1):13, 2023
- *Current Sleep Medicine Reports* vol. 11, art. 10, 2025
- JAMA Pediatrics 2020 — Association of Delaying School Start Time

---

## 2. Дашборд-фічі від інших проєктів — що варто адаптувати

### 2.1 Pomodoro built-in timer
**Хто робить:** Forest, TickTick, Habitica, ClickUp. Не змушує користувача гуглити third-party.
**Як додати:** простий setTimeout-таймер на сторінці «Сьогодні», 2 режими (25/5 і 52/17), кнопки Start/Pause/Reset, аудіо-сигнал при завершенні. ~80 LOC.

### 2.2 Streak insurance / freeze token (Beeminder, Streaks app, Duolingo)
**Чому:** loss aversion (Kahneman-Tversky 1979) працює, але "1 пропуск = 0 streak" — занадто жорстко і викликає demotivation на 2-й місяць. Рішення: 1 freeze token на тиждень.
**Як додати:** state.freezeTokensLeft (max 1, ресет щотижня). Кнопка «Використати freeze» якщо сьогодні нічого не зробив. ~30 LOC.

### 2.3 `.ics` calendar export
**Чому:** імпорт дат іспитів у Google Calendar / Apple Calendar — користувач отримує notifications автоматично без сторонніх інструментів.
**Як додати:** функція що генерує .ics blob з усіма ключовими датами (PKPZJP March 2028, SAS UK 2028, mock-tests). ~50 LOC.

### 2.4 Failure journal / Antifragile log (Taleb)
**Чому:** writing about failures + 1 protective action per week → меньше повторних помилок. Schippers et al. *Frontiers in Psychology* 2015 (вже згаданий) — reflective writing +22% академічної успішності.
**Як додати:** окрема субсторінка «Журнал невдач», 4 запитання щотижня (що пішло не так / чому / який сигнал я пропустив / 1 захисна дія), список записів у state. ~150 LOC.

### 2.5 Mock-test scheduler (countdown 100/60/30/14/7 днів)
**Чому:** літературою затверджена практика — інкрементальна, наростаюча інтенсивність mock-test-ів. 3 безкоштовні офіційні тести C1 з certyfikatpolski.pl (2017, 2019, 2020) — використовуй: тест 1 на 12-mes mark, тест 2 на 6-мес, тест 3 на 1-мес.
**Як додати:** computed countdown на сторінці «Сьогодні», список майбутніх mock-test дат, можливість позначити «mock-test зроблено».

### 2.6 Print-friendly CSS (@media print)
**Чому:** інколи треба роздрукувати weekly plan на тиждень — особливо коли інтернет/світло вимкнено.
**Як додати:** CSS @media print { ... } — приховати sidebar/nav, показати тільки tasks-list дня. ~30 LOC.

### 2.7 Chronotype setting (див. 1.6)
**Як:** input в Налаштування → 1 рядок у "поточний фокус" блоку.

---

## 3. Що подивився і НЕ беру

| Фіча | Звідки | Чому не беру |
|---|---|---|
| XP / level / gold | Habitica | Gamification → addictive, але studied effects mixed. Підліток і так має достатньо dopamine triggers (TikTok). Додавання XP в дашборд може зменшити intrinsic motivation (Deci & Ryan SDT). |
| Monetary commitment | Beeminder | Підліток без credit card. Концепт пенальті — так, реалізація через гроші — ні. |
| Skill tree (WaniKani) | WaniKani | Не підходить для мови з нуля — WaniKani це для японської з 6000 kanji. PL/SK не має такої древоподібної структури. |
| Public leaderboard | Duolingo | Соціальний тиск може допомогти, але потребує бекенду. Цей дашборд — local-only. |
| Spaced-rep граф-візуалізація (heatmap, like GitHub) | GitHub contributions, Anki heatmap addon | Корисно, але високий cost (~100 LOC). У наявному дашборді вже є streak counter — достатньо. Можна додати в v5. |
| BRAC / 90-min ultradian rhythm | Kleitman 1969 | Kerkhof 1985, Conroy & Mills 1995 — meta-analysis не знаходить evidence для 90-min циклу в alertness. Це психологічний міф, не факт. |
| «Learning styles» (VARK) | Educational pop psych | Pashler et al. 2008 — псевдонаука. Вже в дебанк-секції. |

---

## 4. План іспиту PL C1 — конкретний timeline

Базується на reality перевірки certyfikatpolski.pl:
- 3 безкоштовних офіційних тестів C1: 2017, 2019, 2020 (можна скачати).
- Курс preparatory от polish institutes — 2-3 місяці до іспиту, ~1900 PLN.
- Структура: listening + reading + grammar + writing (4 секції).

**Рекомендація:**
- **12 міс до C1 (травень 2027):** mock-test з 2017 (test 1), час exam-conditions. Очікуваний результат: 35-50%.
- **6 міс до C1 (листопад 2027):** mock-test з 2019 (test 2). Ціль: 55-65%.
- **3 міс до C1 (січень 2028):** mock-test з 2020 (test 3). Ціль: 65-75%.
- **30 днів до:** mock з old test 1 повторно. Ціль: 75%+.
- **7 днів до:** ніяких mock — sleep, light review only.

---

## 5. План SK B1 — конкретний timeline

SAS UK Bratislava — 4 секції (listening / reading / grammar / writing + speaking).
SCIO NPS — окремий тест, не обов'язковий (FactCheckR caveat вже в плані).

Менш надано безкоштовних mock-тестів. Compensation strategy:
- Slovake.eu має quizzes per lesson.
- Через 6 міс A2 → italki teacher 1×/тиждень for speaking.
- Не починати speaking партнера до A2 — буде painful, не efficient.

---

## 6. Implementation plan — що пушу в код

Порядок (за пріоритетом):

1. ✅ Створив RESEARCH_v4.md
2. ⏳ 6 нових технік-карт у Методи & Дисципліна (FSRS, Method of Loci, CLT, Deep Work, 52/17, Chronotype)
3. ⏳ Pomodoro/52-17 timer (Today page)
4. ⏳ Failure journal subpage
5. ⏳ `.ics` calendar export
6. ⏳ Mock-test scheduler з countdown
7. ⏳ Chronotype setting + "peak slot" badge
8. ⏳ Print-friendly CSS
9. ⏳ Manual test (Chrome) → PR

Якщо щось не влізе у scope — переноситься на v5.
