# Test Plan v4 — adversarial, focused on what actually changed

Scope: PR #4 додає 6 фіч до існуючого dual-track роадмапу. Тестуємо саме нові пов'язані з кодом речі — не регресую старі.

**Тестовий шлях:** локальний `python3 -m http.server 8765` → Chrome → `http://localhost:8765/roadmap.html`. Стан localStorage чистимо перед потрібними тестами через DevTools.

**Адверсаріальний принцип:** для кожного assertion питаю «якщо код зламаний — це виглядатиме інакше?». Якщо ні — переписую тест.

---

## Test 1 · Pomodoro timer — Start counts down, mode switch resets

**Дії:**
1. Відкрити `#today`, скрол до «Фокус-таймер».
2. Прочитати display = `25:00`, phase text = «Робота · 25-5 · пауза».
3. Натиснути **Start**.
4. Чекати 5 секунд, прочитати display.
5. Натиснути **Pause**, прочитати display.
6. Перемкнути на пресет **52/17**.
7. Прочитати display.

**Pass criteria:**
- Після Step 3, через 5 секунд: display показує `24:54` або `24:55` (НЕ `25:00` — це довів би що інтервал не зареєстрований).
- Після Step 5 (Pause): display заморожений на значенні з step 4, phase text містить «пауза».
- Після Step 6 (mode switch): display = `52:00` (НЕ `24:5x`). Підтверджує що `pomoSetMode` правильно reset-ить remaining.

**Чому broken-implementation відрізнявся б:** якщо `setInterval` не реєструється — display залишиться `25:00`. Якщо mode switch не зчитує `POMO_PRESETS` — буде або 0:00, або старе значення.

---

## Test 2 · Mock countdown — порахуй дні правильно

**Дії:**
1. Відкрити DevTools console, очистити localStorage: `localStorage.clear(); location.reload();`.
2. Прочитати картку «Peak slot і mock-тести» на `#today`.
3. Виписати дату першого mock (Mock #1 PL) і виписане число днів.
4. Власноруч порахувати: `(2027-03-15) - (2026-05-17) = ?` днів. Очікую 302 ± 1 (буде ±1 через timezone і округлення).

**Pass criteria:**
- Mock #1 PL дата = `2027-03-16` (тобто 365 днів до `2028-03-15`, мінус mock_offset 365 — це генерується з `pl_exam='2028-03-15'`).
- Виписане «N дн.» різниця між сьогодні (2026-05-17) і `2027-03-16` = в межах **301-304 днів**.
- 5 рядків: 4 mocks + 1 exam (PL C1 іспит).
- PL C1 іспит = `668 дн.` (для 2028-03-15 від 2026-05-17 = 668 ± 1).

**Чому broken-implementation відрізнявся б:** якщо `daysUntil` повертає мс замість днів — побачимо 7-значне число. Якщо arithmetic off — будуть негативні значення для майбутніх дат, або всі 0.

---

## Test 3 · Settings → змінити PL exam дату → countdown оновлюється

**Дії:**
1. Перейти на `#settings`.
2. Знайти «Mock-test schedule + .ics export».
3. У полі «PL C1» змінити дату на `2030-01-01` через date-picker.
4. Перейти на `#today`, прочитати mock countdown.

**Pass criteria:**
- Toast: «Дату збережено».
- На `#today` Mock #1 (PL) дата = `2029-01-01` (тобто `2030-01-01 - 365д`).
- PL C1 іспит = `1325 дн.` приблизно (від 2026-05-17 до 2030-01-01 = 1325 ± 1).

**Чому broken-implementation відрізнявся б:** якщо `onchange` не привʼязаний — дата НЕ зберігається. Якщо `renderMockCountdown` не пересмикається — старе значення залишиться.

---

## Test 4 · .ics export — валідний VCALENDAR

**Дії:**
1. Перейти на `#settings`, скрол до «Mock-test schedule».
2. Натиснути **«Скачати .ics (всі дати)»**.
3. Відкрити завантажений файл через `cat /home/ubuntu/Downloads/roadmap_exams_*.ics` в shell.

**Pass criteria:**
- Файл починається з `BEGIN:VCALENDAR` і закінчується `END:VCALENDAR`.
- Містить рядок `VERSION:2.0`.
- Містить мінімум 7 `BEGIN:VEVENT`: 3 іспити (PL/SK/NMT) + 4 mocks для активного треку.
- Кожен VEVENT має `DTSTART;VALUE=DATE:YYYYMMDD` формат (8 цифр).
- Один з SUMMARY містить «PL C1» або «PKPZJP».

**Чому broken-implementation відрізнявся б:** якщо `generateICS` зламаний — файл буде порожній, або з невалідною структурою. Імпорт у Google Calendar обламається.

---

## Test 5 · Failure Journal — додати, побачити, видалити

**Дії:**
1. Перейти на `#body`, клікнути на subtab **«Журнал невдач»**.
2. У формі «Додати запис» заповнити всі 4 поля унікальними значеннями (наприклад: «test what 12345», «test why», «test signal», «test action»).
3. Натиснути **«Зберегти запис»**.
4. Прочитати картку «Історія (N)».
5. Розкрити `<details>` запису, прочитати поля.
6. Натиснути **«Видалити»** на цьому записі.
7. F5 (refresh) → перевірити що запис видалений зберігається після refresh.

**Pass criteria:**
- Після Step 3: counter «(0)» → «(1)». Toast «Запис додано».
- Поля очищені (порожні після save).
- У `<details>` summary видно дату (2026-05-17) + «test what 12345».
- При розкритті — всі 4 поля з правильними значеннями.
- Після Step 6: counter «(1)» → «(0)». Toast «Запис видалено».
- Після Step 7 (F5): counter все ще «(0)» — стан переживає reload.

**Чому broken-implementation відрізнявся б:** якщо `save()` не викликаний — після F5 counter знову буде 1. Якщо escapeHtml зламаний — будуть HTML entities у відображенні. Якщо event listener не призначений — onclick дасть жодного ефекту.

---

## Test 6 · Chronotype peak-slot badge — змінити в Settings → відобразити

**Дії:**
1. Перейти на `#settings`, змінити Chronotype select на **«Morning»**.
2. Toast «Chronotype оновлено».
3. Перейти на `#today`, прочитати peak slot badge.

**Pass criteria:**
- Badge містить текст `morning · peak: 8-11, 14-16`.
- Якщо поточний час між 8-11 або 14-16 (UTC у тестовому середовищі) — badge стане зеленим зі словом «★ Пік-слот зараз».

**Чому broken-implementation відрізнявся б:** якщо PEAK_SLOTS не зчитується — badge показуватиме evening (старе) або порожній.

---

## Що НЕ тестую (свідомо)

- **Phase-end beep** Pomodoro: треба чекати 25 хв повного циклу — нереально під час тестування. Покривається code-review: `pomoBeep` через Web Audio API.
- **Імпорт .ics у Google Calendar**: потребує Google акаунту і ручної дії. Перевіряю валідність структури .ics, цього достатньо для довести працездатність формату.
- **Регресія v3 функцій (BOTH прибрано, 14-day lockout, 18 технік)**: тестовано в PR #3. Якщо не зламано — буде проходити.
- **Mobile responsive**: dashboard перевірено в попередніх PR, нові v4 cards використовують той самий `grid cols-2` що адаптується.

---

## Ризики виявлені під час складання плану

1. **Date timezone**: `daysUntil` використовує `T12:00:00` — це може дати ±1 день розбіжності залежно від UTC offset тестового середовища. Acceptable у межах ±1.
2. **Empty mockSchedule після reset-all**: `defaults.mockSchedule` встановлюється в init, але якщо reset-all викликається — clone(defaults) знову створює. Перевірю в Test 2 step 1.
