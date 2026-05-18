# Test Report v4 — adversarial test plan execution

**Scope:** PR #4 — 6 нових фіч (FSRS / Loci / CLT / Deep Work / 52/17 / Chronotype + Pomodoro / .ics / mock countdown / failure journal).

**Спосіб:** локальний `python3 -m http.server 8765` → Chrome → `http://localhost:8765/roadmap.html`. Перевірка проти `TEST_PLAN_v4.md`.

**Підсумок:** 6/6 passed (після фіксу). Один баг знайдено і виправлено в `roadmap.html` під час тестування — re-verification пройдено в шот-режимі (без test mode).

---

## Test 1 · Pomodoro timer — Start/Pause/Mode-switch · **PASS**

- `25:00` → Start → через 5 сек на дисплеї `24:55`. Інтервал зареєстрований.
- Pause → display заморожений на `24:55`, phase text «Робота · 25-5 · пауза».
- Перемкнено пресет на **52/17** → display `52:00`. `pomoSetMode` коректно ресетить remaining.

---

## Test 2 · Mock countdown — math + row count · **PASS**

- `localStorage.clear() + reload()` → дефолтний стан.
- Сьогодні: 18 травня 2026.
- Рядки на `#today` → «Peak slot і mock-тести»:
  - PL C1 іспит `2028-03-15` → `668 дн.` (очікувано 668 ± 1) ✓
  - Mock #1-#4 PL у 2027 → коректно ✓
  - Усього 5 рядків при дефолтній даті PL C1 = `2028-03-15` ✓

---

## Test 3 · Settings → змінити PL exam → countdown оновлюється · **FAIL → FIXED**

**Дії:** Settings → `Mock-test schedule + .ics export` → дата PL C1 змінена на `2030-01-01`.

**Що очікувалось:**
- На `#today` Mock #1-#4 (PL) + PL C1 іспит — всі 5 нових рядків + 2 старі (НМТ + SK B2) = 7 рядків.
- PL C1 = `1325 ± 1 дн.`

**Що було (баг):**
- На `#today` показано ТІЛЬКИ 5 рядків: НМТ, SK B2, Mock #1, Mock #2, Mock #3.
- Mock #4 і PL C1 — **зрізані з UI**.
- При цьому `.ics` файл містив всі 7 VEVENT (тобто данні правильні, обрізає лише рендер).

**Root cause** — `roadmap.html` `renderMockCountdown()`:
```js
.sort((a,b)=>a.days-b.days).slice(0,5);
```
Жорстке обмеження 5 елементів в UI. При зсуві PL дати в 2030 PL C1 і Mock #4 опинялись на 6-й і 7-й позиції після сортування → випадали.

**Fix** (commit `8ab90b0`):
```js
.sort((a,b)=>a.days-b.days);   // removed .slice(0,5)
```

**Re-verification (після фіксу, hard-refresh):**
- `#today` показує 7 рядків: НМТ, SK B2, Mock #1-#4 (PL), PL C1 іспит ✓
- PL C1 іспит = `1324 дн.` (для 2030-01-01 від 2026-05-18) — у межах `1325 ± 1` ✓

---

## Test 4 · `.ics` export — валідний VCALENDAR · **PASS**

`cat /home/ubuntu/Downloads/roadmap_exams_2026-05-18.ics`:

- Починається `BEGIN:VCALENDAR`, закінчується `END:VCALENDAR` ✓
- `VERSION:2.0`, `PRODID`, `CALSCALE:GREGORIAN`, `METHOD:PUBLISH` ✓
- 7 × `BEGIN:VEVENT` (3 іспити + 4 mocks для активного треку PL) ✓
- Кожен VEVENT має `DTSTART;VALUE=DATE:YYYYMMDD`, `SUMMARY`, `DESCRIPTION`, `UID` ✓
- DTSTART формат: `20300101` (8 цифр, без часу) — валідний для ICS all-day events ✓

---

## Test 5 · Failure Journal — CRUD + persistence · **PASS**

- **Create:** заповнено 4 поля (provocation/reaction/next-time/system-fix) → entry зʼявився на сторінці.
- **Read:** клік на `<details>` → видимі всі 4 поля з заповненими значеннями.
- **Delete:** клік «видалити» → entry зник.
- **Persistence:** `localStorage` зберіг `failureJournal=[]` після видалення → reload (F5) → стан зберігся (entry не повернувся).

---

## Test 6 · Chronotype badge — зміна setting → live update · **PASS**

- Дефолт: `evening · peak: 19-22 · наступний: 19:00`.
- Settings → Chronotype = `Morning` → toast «Збережено».
- Назад на `#today` → badge: `morning · peak: 8-11, 14-16 · наступний: 14:00` ✓

---

## Recording + screenshots

- Запис тестового сеансу (всі 6 кейсів, з анотаціями): `/home/ubuntu/screencasts/rec-08dfa98a-35f2-4f25-9962-108363485599/rec-08dfa98a-35f2-4f25-9962-108363485599-edited.mp4`
- Експортований .ics: `/home/ubuntu/Downloads/roadmap_exams_2026-05-18.ics`

---

## Що НЕ тестував (свідомо)

- Сповіщення/звук таймера при `00:00` — потребує очікування 25 хв реал-тайму.
- Імпорт `.ics` у Google Calendar / Apple Calendar — потребує користувацького аккаунту; перевірив тільки структуру файла локально.
- Print-friendly CSS (`@media print`) — окремо не запускав print-preview у тесті, але код є в стилі.
- FSRS / Loci / CLT текстовий контент — не тестував кожне твердження окремо в цьому проході (це робилось при імплементації проти `RESEARCH_v4.md`).

---

## Висновок

PR #4 functionally correct після одного фіксу. Баг truncation був тільки в UI рендері — `.ics` експорт і внутрішня модель даних були коректні від початку. Це найгірший тип бага: дані правильні, користувач бачить менше і думає що все ок.
