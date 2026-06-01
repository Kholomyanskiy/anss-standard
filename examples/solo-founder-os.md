# ANSS-CORE — Solo Founder OS
## Локальная операционная система солофаундера с AI-командой

**Версия шаблона:** 1.0 (ANSS-Core)
**Уровень:** CORE

> **Агент читает этот документ так:**
> 1. Прочитай весь документ целиком
> 2. Прочитай ГЛОССАРИЙ (Раздел 2.1) ещё раз — это язык проекта
> 3. Запомни ИНВАРИАНТЫ (Раздел 2.5) — нарушать запрещено никогда
> 4. Прочитай ПРИНЦИПЫ (Раздел 2.6) — компас при неопределённости
> 5. Выполни Agent Review (Раздел 14) ПЕРЕД написанием кода

---

# РАЗДЕЛ 0. МЕТАДАННЫЕ

| Поле | Значение |
|---|---|
| Проект | Solo Founder OS |
| Версия | 1.0 Draft |
| Дата | 2026-06-01 |
| Статус | Draft |
| Автор | Артём Холомянский |
| Заказчик | Артём Холомянский (персональный инструмент) |
| Исполнитель | Claude Code |

**История версий:**

| Версия | Дата | Изменение |
|---|---|---|
| 0.1–0.4 | 2025–2026 | Итеративные черновики промпт-формата |
| 2.5 | 2026-05 | Добавлен Smart Onboarding (промпт-формат) |
| 1.0 | 2026-06-01 | Переработка по стандарту ANSS-Core |

**Уровень детализации:** CORE
*(Small + Low criticality — персональный локальный инструмент)*

---

# РАЗДЕЛ 1. КОНТЕКСТ И ЭКОНОМИКА

## 1.1 Суть проекта

**Что строим:**
Локальное веб-приложение — операционная система для солофаундера с персональной AI-командой из специализированных агентов (CEO, backend, sales, smm и др.) с общей памятью, режимом планирования и файловой системой.

**Проблема которую решаем:**
Нет единого рабочего места для AI-агентов: каждый чат в Claude отдельный, нет памяти между сессиями, нет специализированных ролей, нет режима "поставить задачу → получить план → выполнить". Переключение контекста убивает продуктивность.

**Почему сейчас:**
Claude Code установлен и авторизован, подписка Claude Pro/Max активна. Инструмент нужен для ежедневной работы прямо сейчас — не когда-нибудь потом.

**Что НЕ делаем в этой версии:**
- Не делаем публичный деплой — только localhost
- Не делаем многопользовательский режим
- Не подключаем Fetch/Playwright/Gmail через UI (toggles есть, UI — нет)
- Не делаем мобильное приложение
- Не делаем экспорт/импорт данных
- Не делаем voice input
- Не делаем pinnedExamples UI
- Не используем npm пакеты — только встроенные Node.js модули

## 1.2 Цели и критерии успеха

**Бизнес-цели:**
- Один инструмент заменяет разрозненные AI-чаты для всех рабочих задач
- Smart Onboarding настраивает персональную команду за ≤ 5 минут
- Режим планирования позволяет делегировать задачу AI-команде одной командой

**KPI — как поймём что успех:**
- Онбординг завершён: < 5 минут от первого запуска до первого проекта
- Старт сервера: `node server.js` → работает без `npm install`
- Отправка задачи агенту: < 3 секунды до первого chunk ответа
- Smart Onboarding анализ: 30–60 секунд, с fallback на ручной режим при ошибке

**Definition of Done проекта:**
1. `node server.js` запускается без ошибок, открывает браузер на localhost:3747
2. Smart Onboarding проходит все 4 шага и создаёт рабочую конфигурацию
3. Чат с агентом работает в stream-режиме
4. Режим планирования создаёт и выполняет план через несколько агентов
5. Данные сохраняются в `~/.solo-founder/` и восстанавливаются после перезапуска

## 1.3 Экономика

**Стоимость:**
- Разработка (CAPEX): $0 — разрабатывается самим пользователем через Claude Code
- Инфраструктура (OPEX): $0 — локальный запуск
- LLM (OPEX): $0 дополнительно — уже оплаченная подписка Claude Pro/Max

**ROI:** немедленный — инструмент для ежедневной работы при нулевых доп. затратах.

---

# РАЗДЕЛ 2. ПРЕДМЕТНАЯ ОБЛАСТЬ

## 2.1 Глоссарий

*Агент читает этот раздел ПЕРВЫМ. Все термины — ниже.*

**Solo Founder OS (SFOS)**
Определение: само это приложение целиком
НЕ путать с: macOS, Windows (ОС в традиционном смысле)
В коде: имя проекта, директория `~/.solo-founder/`

**Агент**
Определение: персонаж с именем, ролью, системным промптом и набором навыков. Выполняет задачи через `claude -p`.
НЕ путать с: Claude Code (инструмент разработки), Claude AI (модель)
В коде: объект в `agents.json`, поля: `id, name, role, systemPrompt, mcpServers, *Enabled`

**Реестр агентов (registry)**
Определение: `agents_registry.json` — мастер-список всех 11 возможных агентов
НЕ путать с: `agents.json` — список только активных агентов
В коде: `agents_registry.json` — читается через `loadRegistry()`

**Активные агенты**
Определение: агенты выбранные при онбординге, которые работают в текущей конфигурации
В коде: `agents.json` — читается через `loadActiveAgents()`

**Boss**
Определение: обязательный агент-оркестратор. Принимает задачи, планирует, координирует команду.
НЕ путать с: пользователем (человеком)
В коде: `id: "boss"`, `required: true`

**Setup (Настройщик)**
Определение: обязательный агент HR. Нанимает/настраивает агентов, подключает MCP.
В коде: `id: "setup"`, `required: true`

**Онбординг**
Определение: первичная настройка системы при первом запуске. Создаёт `company.md`, выбирает агентов, создаёт первый проект.
В коде: `state.onboardingDone`, `state.onboardingMode`

**Smart Setup**
Определение: режим онбординга с AI-анализом. Пользователь описывает себя → Boss + Setup генерируют персональную конфигурацию.
В коде: `state.onboardingMode = 'smart'`

**Manual Setup**
Определение: режим онбординга с ручным выбором агентов из готового списка.
В коде: `state.onboardingMode = 'manual'`

**Проект**
Определение: рабочее пространство с именем, контекстом, задачами и артефактами. Привязан к папке на диске.
В коде: `projects/{projectId}/` — `info.json`, `context.md`, `decisions.md`, `done.md`

**Задача (Task)**
Определение: единица работы в рамках проекта. Может быть простым сообщением или планом с шагами.
В коде: `projects/{projectId}/tasks/{taskId}.json`, статусы: `draft → plan_pending → plan_running → done → failed`

**Артефакт**
Определение: файл созданный агентом в процессе выполнения задачи.
В коде: `projects/{projectId}/artifacts/`

**MCP (Model Context Protocol)**
Определение: протокол расширения возможностей Claude через внешние инструменты (filesystem, gmail, fetch, browser).
В коде: `--mcp-config {path}`, временный JSON файл `mcp_configs/{agentId}_mcp.json`

**preferSubscription**
Определение: флаг в state.json означающий что система работает через подписку, не через API ключ.
В коде: `state.preferSubscription: true` → удалять `ANTHROPIC_API_KEY` из окружения перед запуском Claude

**company.md**
Определение: краткое описание деятельности пользователя (макс 1500 символов). Добавляется в контекст каждого агента.
В коде: `~/.solo-founder/company.md`

**Память агента**
Определение: персональный контекст агента (до 800 символов). Добавляется в промпт при каждом вызове.
В коде: `~/.solo-founder/agents_memory/{agentId}.md`

**SSE (Server-Sent Events)**
Определение: механизм стриминга событий от сервера к UI в реальном времени.
В коде: `GET /events` — единый поток событий для всего UI

**taskId**
Определение: уникальный идентификатор задачи, генерируется только через `generateTaskId()`.
НЕ путать с: projectId (UUID проекта)
В коде: формат `task_{Date.now()}_{random5chars}`

## 2.2 Пользователи

**Роль: Солофаундер (единственный пользователь)**
- Кто это: Артём Холомянский, AI-автоматизатор и консультант
- Что делает в системе: ставит задачи агентам, управляет проектами, читает ответы, работает с планами
- Что ценит: скорость запуска, отсутствие настройки, персональная конфигурация
- Ожидаемое количество: 1
- Частота использования: daily
- Технический уровень: высокий

## 2.3 Ограничения и допущения

**Технические ограничения (нельзя менять):**
- Только Windows (path.join, shell:true обязательны)
- Только встроенные Node.js модули — никакого `npm install`
- Три файла: `server.js`, `index.html`, `agents_registry.json`
- Работает через Claude CLI (`claude -p`), не через API
- Порт: 3747 (фиксирован)
- Данные: `~/.solo-founder/` (фиксировано)

**Бизнес-ограничения:**
- Подписка Claude Pro/Max уже оплачена
- Разработка через Claude Code (AI-агент)
- Срок: нет жёсткого дедлайна

**Допущения:**
- `claude` CLI установлен и авторизован
- Node.js установлен
- Пользователь работает на Windows
- MCP filesystem доступен через `npx @modelcontextprotocol/server-filesystem`

**Что принимается как данное:**
- `preferSubscription: true` всегда
- Boss и Setup — всегда обязательные агенты
- Данные хранятся локально, без шифрования (приемлемо для персонального инструмента)

## 2.4 Технологический стек

| Слой | Технология | Версия |
|---|---|---|
| Backend | Node.js (встроенные модули: http, fs, path, os, child_process) | 18+ |
| Frontend | HTML5 / CSS3 / JS ES2022+, ES Modules, без фреймворков | — |
| Хранилище | Файловая система (JSON + Markdown) | — |
| AI | Claude CLI (`claude -p`) через subprocess | Claude Pro/Max |
| MCP | @modelcontextprotocol/server-filesystem (через npx) | актуальная |
| Запуск | start.bat / node server.js | — |

**Внешние зависимости:**

| Сервис | Назначение | Fallback при недоступности |
|---|---|---|
| Claude CLI | выполнение запросов к AI | показать auth_error в UI |
| npx + MCP | filesystem инструменты для агентов | агент работает без MCP (только текст) |

## 2.5 ИНВАРИАНТЫ ✅

*Абсолютные ограничения. Нарушать запрещено при любых обстоятельствах.*
*Агент останавливается если задача требует нарушения инварианта.*

```
INV-001: Нет внешних зависимостей
Нельзя: добавлять npm пакеты или require() внешних модулей
Причина: пользователь должен запустить node server.js без npm install
Проверка: в server.js нет строк кроме встроенных Node.js модулей

INV-002: Три файла
Нельзя: создавать дополнительные серверные файлы (utils.js, config.js и т.д.)
Причина: вся логика в трёх файлах — принципиальное архитектурное решение
Проверка: проект содержит только server.js, index.html, agents_registry.json

INV-003: Работа через подписку
Нельзя: использовать ANTHROPIC_API_KEY при preferSubscription:true
Причина: подписка, не API — принципиальное требование пользователя
Проверка: spawnClaude() удаляет ANTHROPIC_API_KEY из env при preferSubscription:true

INV-004: Обязательные агенты
Нельзя: убирать boss и setup из активных агентов
Причина: система не работает без оркестратора и настройщика
Проверка: finalizeOnboarding() гарантирует boss и setup в результирующем списке

INV-005: Лимиты памяти
Нельзя: сохранять память агента > 800 символов, контекст проекта > 1000 символов
Причина: контрольное окно Claude, предсказуемость промптов
Проверка: saveAgentMemory() обрезает до 800 символов перед записью

INV-006: Единая генерация taskId
Нельзя: генерировать taskId иначе кроме как через generateTaskId()
Причина: уникальность и предсказуемый формат для парсинга
Проверка: `task_${Date.now()}_${Math.random().toString(36).substring(2,7)}`

INV-007: Единый JSON парсер
Нельзя: дублировать логику парсинга JSON в разных местах кода
Причина: extractJson() — единая функция для плана И онбординга (4 стратегии)
Проверка: нет дублирующих блоков try/catch для JSON.parse кроме extractJson()

INV-008: Единый spawnClaude
Нельзя: создавать второй способ запуска claude subprocess
Причина: единая точка для управления auth, timeout, preferSubscription
Проверка: весь запуск claude только через spawnClaude()
```

## 2.6 АРХИТЕКТУРНЫЕ ПРИНЦИПЫ ✅

*Руководство для решений при неопределённости.*

```
AP-001: Simple > Clever
Простое решение которое работает лучше умного которое сложно поддерживать.
При выборе между элегантным и понятным — выбирай понятное.

AP-003: Buy Before Build
Сначала проверь существующие решения. Claude уже есть — не строй LLM runtime.

AP-004: Human Override Always Available
Всегда есть кнопка "пропустить", "отменить", "выбрать вручную".
Нет автономных необратимых действий без возможности отмены.

AP-006: Fail Loud
Лучше явная ошибка чем тихая неправильная работа.
Auth error → сразу в UI. Анализ упал → fallback + уведомление.

AP-007: Data Outlives Code
Данные в ~/.solo-founder/ важнее кода. Никогда не удалять данные пользователя.

AP-009: YAGNI
Не реализуй то чего нет в спецификации.
pinnedExamples, voice input, multi-user — не в этой версии.

AP-011: Локальность > Зависимости
Встроенные Node.js модули предпочтительнее любых npm пакетов.
Если что-то можно сделать через fs, http, path — делай так.

AP-012: Fallback обязателен для AI
Каждый AI-вызов который может упасть обязан иметь fallback.
Smart Onboarding упал → ручной режим. Парсинг плана упал → manual_chat.
```

---

# РАЗДЕЛ 3. ФУНКЦИОНАЛЬНЫЕ ТРЕБОВАНИЯ

## 3.1 Обзор функциональности

**Функциональные блоки (MoSCoW):**

| Блок | Приоритет |
|---|---|
| Smart Onboarding (4 шага) | Must Have |
| Ручной онбординг (fallback) | Must Have |
| Чат с агентом (stream) | Must Have |
| Режим планирования (Boss → план → выполнение) | Must Have |
| Создание и переключение проектов | Must Have |
| Память агентов и проектов | Should Have |
| Артефакты (автодетект и сохранение) | Should Have |
| Браузер папок (GET /browse) | Should Have |
| История чата (index.json + восстановление) | Should Have |
| Найм / Увольнение агентов | Could Have |
| Браузерные уведомления | Could Have |
| Fetch/Playwright/Gmail UI (toggles) | Won't Have (MVP) |
| pinnedExamples UI | Won't Have (MVP) |
| Voice input | Won't Have (MVP) |
| Экспорт / Импорт данных | Won't Have (MVP) |

**Фазы:**
- Фаза 1 (MVP): блоки Must Have + Should Have
- Фаза 2: Could Have + остальные

## 3.2 User Stories

```
US-001: Smart Onboarding
Как солофаундер
Я хочу рассказать о себе и получить персональную AI-команду
Чтобы не настраивать каждого агента вручную

Acceptance Criteria:
- [ ] Экран выбора режима показывается при первом запуске (onboardingDone:false)
- [ ] Кнопка "Smart Setup" открывает textarea с счётчиком 0/3000 символов
- [ ] Нажатие "Анализировать" отправляет POST /onboarding/analyze и показывает loader
- [ ] Во время анализа показываются два прогресс-бара (Boss + Setup)
- [ ] После onboarding_done показываются карточки агентов с возможностью редактирования
- [ ] Нажатие "Принять команду" завершает онбординг и переходит к главному экрану
- [ ] Все 8 шагов финализации выполняются (company, agents, memory, project, state)

Edge Cases:
- Если оба AI-вызова упали → SSE onboarding_error { fallback:true } → показать ручной выбор
- Если только один упал → использовать результат другого + дефолты
- Если boss/setup не попали в recommendedAgents → добавить принудительно
- Если пользователь нажал "Пропустить" → переход на ручной онбординг
```

```
US-002: Ручной онбординг
Как солофаундер
Я хочу выбрать агентов вручную из готового списка
Чтобы начать работу быстро без AI-анализа

Acceptance Criteria:
- [ ] Грид 11 агентов с чекбоксами
- [ ] Boss и Setup — disabled, всегда checked
- [ ] POST /agents сохраняет выбранных + обязательных агентов
- [ ] Создаётся первый проект с именем и рабочей папкой
- [ ] state.onboardingDone:true после завершения

Edge Cases:
- Если пользователь снял все чекбоксы кроме обязательных → сохранить только boss + setup
```

```
US-003: Чат с агентом
Как солофаундер
Я хочу отправить сообщение агенту и получить ответ в реальном времени
Чтобы работать с AI-командой как с реальными специалистами

Acceptance Criteria:
- [ ] Loader показывается сразу после отправки (до первого chunk)
- [ ] Ответ стримится по мере генерации (agent_chunk события)
- [ ] После agent_done loader скрывается
- [ ] История сохраняется в chatHistories[agentId]
- [ ] company.md и память агента включены в промпт
- [ ] При auth_error показывается инструкция для переавторизации

Edge Cases:
- Если response пустой → показать "Агент не ответил. Попробуй снова."
- Если timeout 120 сек → agent_error с предложением повторить
- Если MCP недоступен → продолжить без MCP (только текст)
```

```
US-004: Режим планирования
Как солофаундер
Я хочу поставить сложную задачу и получить план с выполнением через нескольких агентов
Чтобы не разбивать задачу вручную

Acceptance Criteria:
- [ ] POST /chat/plan → Boss создаёт план в JSON формате
- [ ] План показывается пользователю с возможностью редактирования (↑↓ шагов)
- [ ] POST /chat/plan/:taskId/start запускает последовательное выполнение
- [ ] Каждый шаг: plan_step → (выполнение) → plan_step_done / plan_step_error
- [ ] plan_done содержит summary от Boss
- [ ] Кнопка retry для упавших шагов

Edge Cases:
- Если extractPlan вернул null → показать кнопки retry и manual_chat
- Если агент для шага не активен → plan_step_skip с объяснением
- Если шаг упал → partial completion, остальные шаги продолжают
```

```
US-005: Управление проектами
Как солофаундер
Я хочу создавать проекты и переключаться между ними
Чтобы держать рабочие контексты разделёнными

Acceptance Criteria:
- [ ] POST /projects создаёт проект с уникальным id и папкой
- [ ] GET /projects возвращает список всех проектов
- [ ] Переключение проекта меняет state.currentProject
- [ ] Контекст проекта (context.md) включается в промпт каждого агента
- [ ] Браузер папок (GET /browse?dir=path) работает для выбора рабочей папки

Edge Cases:
- Если workFolder не существует → создать директорию автоматически
- Если projectId=null → getProjectWorkFolder() возвращает os.homedir()
```

## 3.3 Детальные требования по модулям

```
МОДУЛЬ: Smart Onboarding
Назначение: персональная настройка AI-команды через AI-анализ профиля пользователя
Связанные инварианты: INV-003, INV-004, INV-007

User Flow:
1. Пользователь открывает приложение первый раз (onboardingDone:false)
2. Экран выбора: Smart Setup | Ручной выбор
3. [Smart] Пользователь вводит описание себя (textarea, 0/3000 символов)
4. Нажимает "Анализировать" → POST /onboarding/analyze → немедленный ok
5. SSE: onboarding_analyzing (boss, setup) → прогресс-бары в UI
6. SSE: onboarding_done → UI показывает карточки агентов с результатами анализа
7. Пользователь редактирует/подтверждает команду
8. POST /onboarding/finalize → company.md, agents.json, memory, project, state
9. Автопереход к главному экрану

Бизнес-правила:
BR-001: Параллельные вызовы анализа
  Boss и Setup запускаются через Promise.allSettled — не последовательно.
  Исключения: нет — всегда параллельно.

BR-002: Гарантия обязательных агентов
  finalizeOnboarding() всегда добавляет boss и setup в список даже если пользователь снял.
  Граничный случай: пользователь снял boss → система добавляет обратно тихо.

BR-003: Fallback при ошибке анализа
  Если оба вызова упали → onboarding_error { fallback:true } → ручной режим.
  Если один упал → результат другого + дефолты для отсутствующих данных.

BR-004: Кастомные агенты на подтверждение
  systemPrompt кастомных агентов генерирует Claude — всегда показывать пользователю перед сохранением.

Входные данные: interviewText (string, до 3000 символов)
Выходные данные: { companyMd, recommendedAgents, customAgents, agentsMemory, systemPromptPatches, projectSuggestion }

Обработка ошибок:
- JSON парсинг упал → extractJson() пробует 4 стратегии → fallback на дефолты
- Timeout 90 сек → runAgentSync reject → onboarding_error + fallback
- Auth error → broadcastAuthError() + onboarding_error

Acceptance Criteria:
- [ ] POST /onboarding/analyze возвращает {ok:true} немедленно
- [ ] Результат приходит через SSE onboarding_done
- [ ] recommendedAgents всегда содержит boss и setup
- [ ] Интервью сохраняется в onboarding_interview.md
- [ ] customAgents ≤ 3 штуки
- [ ] Патчи промптов добавляются через \n\nДОП. КОНТЕКСТ:\n
```

```
МОДУЛЬ: Agent Runner
Назначение: единая точка запуска Claude subprocess для всех режимов
Связанные инварианты: INV-003, INV-008

Функции:
- buildPrompt(agent, userMessage, projectId, history) — формирует полный промпт
  null-safe: если projectId=null → os.homedir(), пустые строки для memory
- buildMcpConfig(agent, workFolder) — временный JSON для --mcp-config
  filesystem: всегда (если в mcpServers)
  fetch/browser/gmail: только если в mcpServers И *Enabled:true
- spawnClaude(agent, prompt, projectId) — единый запуск
  preferSubscription:true → удалять ANTHROPIC_API_KEY из process.env перед spawn
- handleStderr(data, agentName) — возвращает bool isAuth
- broadcastAuthError() — единый broadcast auth_error для обоих режимов
- runAgentStream(agent, prompt, projectId, onChunk, onDone, onError) — для UI
  loader в UI обязателен независимо от наличия chunks (стриминг буферизуется)
  timeout: 120 секунд
- runAgentSync(agent, prompt, projectId, maxRetries=2) — для плана и онбординга
  exponential backoff: 2000ms * attempt
  timeout: 90 секунд

Бизнес-правила:
BR-005: Стриминг и loader
  Loader показывается сразу при отправке и скрывается только после agent_done.
  Причина: CLI буферизует вывод, первый chunk может прийти через 5-10 секунд.

BR-006: Auth обработка
  handleStderr детектирует auth ошибку → isAuth:true → broadcastAuthError() → остановить выполнение.
  Не retry на auth ошибку.

Acceptance Criteria:
- [ ] spawnClaude удаляет ANTHROPIC_API_KEY при preferSubscription:true
- [ ] handleStderr возвращает bool, не бросает исключение
- [ ] runAgentSync делает maxRetries попыток с exponential backoff
- [ ] Loader показывается до первого chunk и скрывается после agent_done
```

```
МОДУЛЬ: Plan Executor
Назначение: декомпозиция задачи через Boss и последовательное выполнение плана
Связанные инварианты: INV-006, INV-007

Константы:
- PLAN_AGENT_IDS = ['backend','frontend','devops','integrator','analyst','sales','qa','smm','lawyer']

Функции:
- extractJson(text) — ОБЩАЯ функция, 4 стратегии:
  1. Прямой JSON.parse
  2. Из markdown блока ```json
  3. Первый JSON объект в тексте
  4. Починка битого JSON (trailing commas, unquoted keys, single quotes)
- extractPlan(text, userMessage) — использует extractJson + fallback по тексту
  fallback: поиск ID и имён агентов в тексте → формирует план из найденных
- createPlan(taskId, userMessage, projectId) — Boss генерирует JSON план
- executePlan(taskId, projectId, startFromStep=0) — последовательное выполнение
  partial completion: если шаг упал → continue с следующего
  plan_done с summary от Boss после завершения

Обработка ошибок:
- extractPlan вернул null → broadcast plan_error + кнопки retry / manual_chat
- Агент из плана не активен → plan_step_skip с объяснением
- Шаг упал → plan_step_error → продолжить следующий шаг

Acceptance Criteria:
- [ ] extractJson не бросает исключение при любом входном тексте
- [ ] extractPlan возвращает null (не бросает исключение) если план не найден
- [ ] Все step.task оборачиваются в String() перед использованием
- [ ] plan_done содержит summary строку
```

```
МОДУЛЬ: Storage
Назначение: все операции чтения/записи данных на диск

Структура данных:
~/.solo-founder/
  company.md                   (макс 1500 символов)
  agents_registry.json         мастер-список (11 агентов)
  agents.json                  активные агенты
  state.json                   { onboardingDone, onboardingMode, currentProject, preferSubscription:true }
  onboarding_interview.md      текст интервью
  agents_memory/
    {agentId}.md               личная память (макс 800 символов)
  mcp_configs/
    {agentId}_mcp.json         временный MCP конфиг
  projects/
    {projectId}/
      info.json
      context.md               (макс 1000 символов)
      decisions.md             (макс 800 символов)
      done.md                  (макс 800 символов)
      artifacts/
      tasks/
        index.json
        {taskId}.json

Функции (все реализованы):
readFileSafe(filepath, fallback=''), writeFileSafe(filepath, content)
getState(), saveState(data)
getCompany(), saveCompany(c)
loadRegistry(), loadActiveAgents(), saveActiveAgents(agents)
getAgentMemory(id), saveAgentMemory(id, c)  ← обрезает до 800 символов
getProjects(), createProject(name, description, workFolder)
getProjectWorkFolder(projectId)  ← null-safe → os.homedir()
getProjectMemory(projectId, file), saveProjectMemory(projectId, file, content)
getTaskIndex(projectId), addToTaskIndex(projectId, task)
saveTask(projectId, task), loadTask(projectId, taskId)
compressMemoryLocal(content, maxChars)

Acceptance Criteria:
- [ ] readFileSafe никогда не бросает исключение (возвращает fallback)
- [ ] saveAgentMemory обрезает content до 800 символов перед записью
- [ ] getProjectWorkFolder(null) возвращает os.homedir()
- [ ] addToTaskIndex вызывается при каждом saveTask
```

## 3.4 Реестр бизнес-правил

| ID | Правило | Приоритет | Инвариант |
|---|---|---|---|
| BR-001 | Boss и Setup запускаются параллельно через Promise.allSettled | High | INV-004 |
| BR-002 | boss и setup принудительно добавляются в финальный список агентов | High | INV-004 |
| BR-003 | Если анализ упал → onboarding_error + fallback на ручной режим | High | — |
| BR-004 | Кастомные агенты от системы показываются пользователю перед сохранением | High | — |
| BR-005 | Loader показывается до первого chunk, скрывается после agent_done | High | — |
| BR-006 | Auth ошибка → broadcastAuthError() → остановить выполнение без retry | High | INV-003 |
| BR-007 | Память агента обрезается до 800 символов при сохранении | Medium | INV-005 |
| BR-008 | taskId только через generateTaskId() | High | INV-006 |
| BR-009 | extractJson() — единая функция для плана и онбординга | High | INV-007 |
| BR-010 | spawnClaude() — единая функция для всех запусков Claude | High | INV-008 |

---

# РАЗДЕЛ 4. НЕФУНКЦИОНАЛЬНЫЕ ТРЕБОВАНИЯ

## 4.1 Производительность

| Метрика | Норма |
|---|---|
| Первый chunk ответа агента | < 5 сек (стриминг буферизуется CLI) |
| Загрузка UI | < 1 сек |
| Сохранение в Storage | < 100ms |
| Анализ Smart Onboarding | 30–60 сек (два параллельных вызова Claude) |
| Первый MCP запуск | 30–60 сек (npx скачивает пакет) |

*При первом MCP запуске: показывать "Загружаю инструменты..." — это норма, не баг.*

## 4.2 Надёжность

| Показатель | Значение |
|---|---|
| Uptime | 100% пока запущен node server.js (локальный процесс) |
| Восстановление данных | Немедленно — данные на диске, читаются при старте |
| Потеря данных | 0 — writeFileSafe синхронный, FS journal |

*При перезапуске сервера: все данные в ~/.solo-founder/ сохраняются.*

## 4.3 AI Performance Budget

| Метрика | Лимит |
|---|---|
| runAgentStream timeout | 120 секунд |
| runAgentSync timeout | 90 секунд |
| runAgentSync maxRetries | 2 попытки с exponential backoff 2000ms |
| Память агента в промпте | ≤ 800 символов (INV-005) |
| Контекст проекта в промпте | ≤ 1000 символов (INV-005) |
| company.md в промпте | ≤ 1500 символов |
| История чата | сжать при > 20 сообщениях (compressHistory) |

---

# РАЗДЕЛ 5. АРХИТЕКТУРА

## 5.1 Обзор

**Тип:** Монолит (3 файла)

**Диаграмма:**
```
[Браузер] ←→ [server.js :3747]
               │
               ├─ GET /events (SSE stream) → push события в реальном времени
               │
               ├─ API endpoints (REST)
               │    └─ Storage (readFileSafe/writeFileSafe)
               │         └─ ~/.solo-founder/ (FS)
               │
               └─ Agent Runner
                    └─ child_process.spawn("claude", ["-p", ...])
                         └─ MCP: --mcp-config {tmpfile}
                              └─ filesystem, gmail, fetch (если *Enabled:true)
```

**Секции server.js (строго в этом порядке):**
```
// === STORAGE ===           Все файловые операции
// === AGENT RUNNER ===      Запуск Claude subprocess
// === PLAN EXECUTOR ===     Декомпозиция и выполнение планов
// === SMART ONBOARDING ===  Анализ и финализация онбординга
// === API ENDPOINTS ===     HTTP обработчики
// === SSE ===               Server-Sent Events
```

**Правила слоёв:**
- Бизнес-логика (планирование, онбординг) — только в server.js
- UI (index.html) — тонкий клиент, только отображение и события
- Storage — только через функции STORAGE секции, не напрямую через fs

## 5.2 API Contracts

**Полный список endpoints:**

```
GET  /events                  SSE поток событий
GET  /state                   { onboardingDone, onboardingMode, currentProject, preferSubscription }
POST /state                   { onboardingDone?, currentProject?, ... }
GET  /company                 text/plain — содержимое company.md
POST /company                 { content: string }
GET  /agents                  [ ...activeAgents ]
POST /agents                  [ ...agentsToSave ] — найм/увольнение
GET  /agents/all              [ ...registry ] — все 11 агентов
GET  /agents/:id/memory       text/plain
POST /agents/:id/memory       { content: string }
GET  /projects                [ ...projects ]
POST /projects                { name, description, workFolder }
GET  /projects/:id/memory/:file   text/plain — file: context|decisions|done
POST /projects/:id/memory/:file   { content: string }
GET  /projects/:id/tasks      [ ...tasks from index.json ]
GET  /projects/:id/tasks/:taskId  task object
POST /chat                    { agentId, message, projectId, history }
POST /chat/plan               { message, projectId } → { taskId }
PUT  /chat/plan/:taskId       { plan } — обновить план перед запуском
POST /chat/plan/:taskId/start { projectId }
POST /chat/continue/:taskId   { agentId, message, projectId }
GET  /browse?dir=path         { dirs: [], files: [] }
GET  /integrations/status     { gmail, fetch, browser: bool }
POST /integrations/toggle     { agentId, integration, enabled }
POST /onboarding/analyze      { interviewText } → { ok: true } немедленно
POST /onboarding/finalize     { companyMd, selectedAgentIds, customAgents, agentsMemory,
                                systemPromptPatches, projectName, projectDescription, workFolder }
                             → { ok: true, projectId, agentsCount }
```

**Формат ошибок:**
```json
{ "error": "ERROR_CODE", "message": "human-readable" }
```

**HTTP коды:**
- 200: успех
- 400: неверные входные данные
- 404: ресурс не найден
- 500: внутренняя ошибка сервера

## 5.3 SSE События

```
// Подключение
connected              { message: "Connected" }

// Агент
agent_status           { agentId, status: 'thinking'|'idle' }
agent_chunk            { agentId, taskId?, chunk: string }
agent_done             { agentId, taskId?, response: string }
agent_error            { agentId, taskId?, error: string }

// Планирование
plan_creating          { taskId }
plan_created           { taskId, plan: [...steps] }
plan_started           { taskId }
plan_step              { taskId, stepIndex, agentId, task }
plan_step_done         { taskId, stepIndex, response }
plan_step_error        { taskId, stepIndex, error }
plan_step_skip         { taskId, stepIndex, reason }
plan_done              { taskId, summary }
plan_error             { taskId, error }

// Аутентификация
auth_error             { message, instruction }

// Smart Onboarding
onboarding_analyzing   { step: 'boss'|'setup', message: string }
onboarding_done        { result: { companyMd, recommendedAgents, customAgents,
                                   projectSuggestion, agentsMemory, systemPromptPatches } }
onboarding_error       { error: string, fallback: bool }
```

## 5.4 Агенты (agents_registry.json)

11 базовых агентов. Все `*Enabled: false` по умолчанию.

| ID | Имя | Роль | Temperature |
|---|---|---|---|
| boss | Алекс | CEO · Архитектор | ~0.7 |
| backend | Макс | Backend Developer | ~0.2 |
| frontend | Артём | Frontend Developer | ~0.4 |
| devops | Игорь | DevOps Engineer | ~0.2 |
| integrator | Катя | Интегратор | ~0.3 |
| analyst | Рома | Бизнес-аналитик | ~0.4 |
| sales | Лера | Продажи · Аутрич | ~0.8 |
| qa | Лена | QA · Ревьюер | ~0.1 |
| smm | Дима | SMM · Контент | ~0.9 |
| lawyer | Юля | Юрист | ~0.2 |
| setup | Настройщик | HR · Настройка | ~0.5 |

`required: true` — boss, setup (нельзя отключить)
`mcpServers` — потенциальные интеграции, активируются только при `*Enabled:true`

## 5.5 ADR + Decision Log

```
ADR-001: Монолит из трёх файлов
Контекст: нужен минимальный деплой без npm install
Решение: весь код в server.js + index.html + agents_registry.json
Причина: пользователь запускает node server.js без любых зависимостей
Trade-offs: один большой файл, но максимальная простота запуска
Для агентов: не создавать дополнительные файлы (INV-002)
```

```
ADR-002: Claude CLI через subprocess, не API
Контекст: пользователь платит за подписку, не хочет API ключ
Решение: child_process.spawn("claude", ["-p", prompt, ...flags])
Причина: preferSubscription:true — принципиальное требование
Trade-offs: нет прямого контроля над токенами и стоимостью
Для агентов: удалять ANTHROPIC_API_KEY перед spawn (INV-003)
```

```
ADR-003: SSE вместо WebSocket
Контекст: нужен односторонний поток событий сервер→клиент
Решение: Server-Sent Events (GET /events)
Причина: встроен в браузер, не нужны внешние библиотеки, достаточно для задачи
Trade-offs: нет двустороннего канала, только сервер→клиент
```

```
DEC-001: Файловая система как БД
Вопрос: где хранить данные (SQLite vs JSON файлы)
Решение: JSON + Markdown файлы в ~/.solo-founder/
Причина: нет npm зависимостей, данные читаемы человеком, легкий debug
Отклонено: SQLite — требует npm пакет (better-sqlite3)
```

```
DEC-002: Promise.allSettled для анализа онбординга
Вопрос: последовательно или параллельно запускать Boss + Setup анализ
Решение: параллельно через Promise.allSettled
Причина: экономим 30-60 секунд ожидания
Отклонено: sequential — слишком долго для UX
```

---

# РАЗДЕЛ 6. БЕЗОПАСНОСТЬ

## 6.1 Аутентификация и авторизация

- Метод: нет (локальное приложение, localhost only)
- Внешних пользователей нет
- PIN в state.json — опциональная фича для защиты от несанкционированного доступа локально (Could Have)

## 6.2 Защита данных

- Шифрование at rest: нет (персональный инструмент, приемлемо)
- Шифрование in transit: N/A (localhost)
- Персональные данные третьих лиц: нет

## 6.3 Управление секретами

- Нет API ключей в коде
- Claude авторизован через CLI (`claude auth login`)
- ANTHROPIC_API_KEY удаляется из env при preferSubscription:true (INV-003)

## 6.4 Compliance

- Персональные данные EU/UK: N/A (персональный инструмент пользователя о себе)
- Платёжные данные: N/A
- AI-система (EU AI Act): Minimal risk — персональный инструмент, нет публичных пользователей

---

# РАЗДЕЛ 7. ИНФРАСТРУКТУРА

## 7.1 Окружения

| Окружение | Данные | Доступ |
|---|---|---|
| Production (единственное) | реальные данные пользователя | только пользователь локально |

*Нет staging или development окружений — персональный инструмент.*

## 7.2 Деплой

- Провайдер: localhost (Windows)
- Запуск: `node server.js` или `start.bat`
- Нет CI/CD, нет автодеплоя
- "Rollback": перезапустить предыдущую версию файлов

## 7.3 Мониторинг

- Логи: console.log в terminal где запущен node server.js
- Алерты: нет (локальный инструмент)
- Индикатор работы: browser tab на localhost:3747

## 7.4 Бэкап

- Данные: `~/.solo-founder/` — пользователь сам отвечает за backup
- Рекомендация: периодически копировать папку в облако

---

# РАЗДЕЛ 8. ТЕСТИРОВАНИЕ

## 8.1 Стратегия

| Уровень | Как | Критерий |
|---|---|---|
| Smoke-тест | Запустить node server.js → открыть браузер | HTTP 200 на localhost:3747 |
| Функциональный | Пройти Smart Onboarding вручную | onboardingDone:true в state.json |
| Интеграционный | Отправить сообщение агенту | agent_done с непустым response |
| Регрессия | Проверить 30 требований из чеклиста | Все пункты выполнены |

## 8.2 Ключевые тест-кейсы

```
TEST-001: Запуск без npm install
Дано:     только node.js установлен, репо склонировано
Когда:    node server.js
Тогда:    сервер стартует без ошибок, браузер открывается на :3747
Инвариант: INV-001
```

```
TEST-002: Smart Onboarding полный цикл
Дано:     onboardingDone:false, claude авторизован
Когда:    пользователь вводит описание и нажимает "Анализировать"
Тогда:    через 30-60 сек появляются карточки агентов с boss и setup в списке
          после "Принять" → state.json { onboardingDone:true, onboardingMode:'smart' }
          agents.json содержит boss и setup
Инвариант: INV-004
```

```
TEST-003: Fallback при ошибке анализа
Дано:     POST /onboarding/analyze отправлен
Когда:    Claude возвращает неправильный JSON
Тогда:    extractJson пробует 4 стратегии → fallback на пустой объект
          UI показывает ручной выбор агентов
Инвариант: INV-007
```

```
TEST-004: Чат с агентом
Дано:     онбординг завершён, агент выбран
Когда:    пользователь отправляет сообщение
Тогда:    loader появляется немедленно
          agent_chunk события приходят в SSE
          loader исчезает после agent_done
Инвариант: — (BR-005)
```

```
TEST-005: Auth ошибка
Дано:     claude разлогинен или нет авторизации
Когда:    пользователь отправляет сообщение агенту
Тогда:    SSE auth_error с инструкцией для переавторизации
          выполнение останавливается (нет retry)
Инвариант: INV-003
```

```
TEST-006: Режим планирования
Дано:     онбординг завершён, есть backend и другие агенты
Когда:    пользователь отправляет задачу через план
Тогда:    Boss создаёт план в JSON
          план показывается в UI
          выполнение проходит по шагам с SSE событиями
          plan_done с summary
```

```
TEST-007: Память агента лимит
Дано:     saveAgentMemory('boss', строка 1000 символов)
Когда:    вызов функции
Тогда:    в agentId.md записано не более 800 символов
Инвариант: INV-005
```

## 8.3 Smoke-тест (после каждого изменения)

```
□ node server.js запускается без ошибок в консоли
□ localhost:3747 открывается в браузере
□ Экран онбординга или главный экран показывается корректно
□ Отправить тестовое сообщение агенту → получить agent_done
□ state.json и agents.json читаются корректно
```

---

# РАЗДЕЛ 9. УПРАВЛЕНИЕ ПРОЕКТОМ

## 9.1 Декомпозиция

| Модуль | Зависит от | Оценка |
|---|---|---|
| Storage (базовые функции) | — | основа |
| Agent Runner | Storage | нужен для всего |
| Ручной онбординг | Storage, Agent Runner | fallback MVP |
| Smart Onboarding | Agent Runner, Storage | ключевая фича |
| Чат с агентом | Agent Runner, Storage | core функция |
| Режим планирования | Agent Runner, extractJson | core функция |
| Управление проектами | Storage | нужен для чата |
| Управление памятью | Storage | Should Have |
| Артефакты | Storage | Should Have |

**Критический путь:** Storage → Agent Runner → Чат → Онбординг → Планирование

## 9.2 Риски

| Риск | Вероятность | Влияние | Митигация |
|---|---|---|---|
| Стриминг буферизуется CLI, chunks задерживаются | High | Low | Loader обязателен независимо от chunks |
| Первый MCP запуск 30-60 сек (npx скачивает пакет) | High | Medium | Показывать "Загружаю инструменты..." |
| Парсинг плана упал даже с 4 стратегиями | Medium | Medium | Кнопки retry и manual_chat обязательны |
| Оба вызова онбординга упали | Low | Medium | Fallback на ручной режим + onboarding_error |
| Кастомные агенты с плохим systemPrompt от Claude | Medium | Low | Показывать пользователю на проверку перед сохранением |

## 9.3 Что явно вне scope

- Публичный деплой и HTTPS
- Многопользовательский режим и аутентификация
- Mobile app / PWA
- Экспорт / Импорт конфигурации
- Voice input
- pinnedExamples UI
- Fetch/Playwright/Gmail управление через UI (toggles в коде есть)
- Бэкап автоматический

---

# РАЗДЕЛ 10. CHANGE SPECIFICATION

*Заполни перед любым изменением существующего кода.*

```
CHANGE: [Название изменения]
Дата: YYYY-MM-DD
Затронутые инварианты: [INV-XXX]

ТЕКУЩЕЕ СОСТОЯНИЕ:
[Как работает прямо сейчас — конкретные функции/файлы]

ЦЕЛЕВОЕ СОСТОЯНИЕ:
[Как должно работать после]

ЧТО НЕ МЕНЯЕМ:
- [функция / endpoint] — не трогать
- [структура данных] — не трогать

IMPACT ANALYSIS:
- Затронутые функции: [список]
- Риски: [что может сломаться]

ROLLBACK:
- Триггер: [при каком условии откатываемся]
- Процедура: восстановить предыдущую версию файла из git/backup

РЕГРЕССИОННЫЕ ПРОВЕРКИ:
- [ ] Smoke-тест прошёл
- [ ] [что проверить после изменения]
```

---

# РАЗДЕЛ 11. AI-FIRST (SDD)

## 11.1 Конфигурация агентов

**Иерархия правил:**
```
1. CLAUDE.md (глобальный) → наивысший приоритет
2. Это ТЗ (SOLO_FOUNDER_OS_ANSS.md) → правила проекта
3. Промпт сессии → оперативные инструкции
Нижний уровень НИКОГДА не отменяет верхний.
```

**Forbidden Patterns этого проекта:**
```
НЕ добавлять require() внешних npm пакетов (INV-001)
НЕ создавать дополнительные .js файлы кроме server.js (INV-002)
НЕ дублировать логику extractJson() (INV-007)
НЕ создавать второй способ запуска Claude subprocess (INV-008)
НЕ удалять или переименовывать секции server.js (STORAGE/AGENT RUNNER/etc.)
НЕ хардкодить путь ~/.solo-founder/ — только через константу DATA_DIR
НЕ генерировать taskId иначе кроме через generateTaskId() (INV-006)
НЕ убирать boss и setup из активных агентов (INV-004)
НЕ использовать ANTHROPIC_API_KEY при preferSubscription:true (INV-003)
```

## 11.2 Fail-Fast условия

```
АГЕНТ ОСТАНАВЛИВАЕТСЯ при:
□ Задаче добавить npm пакет
□ Задаче создать дополнительный .js файл
□ Обнаружении дублирующей логики extractJson/spawnClaude
□ Необходимости нарушить любой INV-XXX
□ Архитектурном выборе не описанном в ТЗ

АГЕНТ ДЕЙСТВУЕТ САМОСТОЯТЕЛЬНО при:
□ Деталях именования переменных
□ CSS стилях и отступах
□ Порядке элементов внутри одной секции
□ Сообщениях об ошибках (текст)
□ Конкретных ASCII-макетах UI (если логика описана)
```

## 11.3 LLM/AI компоненты

Solo Founder OS использует AI как инструмент выполнения (не как core продукт):

| Агент | Temperature | Назначение |
|---|---|---|
| boss | ~0.7 | планирование, координация |
| backend, devops, qa, lawyer | ~0.1–0.2 | точные технические задачи |
| integrator, analyst, frontend | ~0.3–0.4 | структурированные задачи |
| sales | ~0.8 | продающие тексты |
| smm | ~0.9 | креативный контент |

**Инварианты AI в этом продукте:**
- Агент не выполняет действие без ответа от Claude subprocess (нет мок-ответов)
- Human Override: пользователь всегда может прервать выполнение плана
- EU AI Act: Minimal risk (персональный инструмент, нет публичных пользователей)

---

# РАЗДЕЛ 12. ЧЕКЛИСТ 30 ТРЕБОВАНИЙ

*Полный список обязательных для реализации пунктов.*

```
□ 1.  preferSubscription:true → удалять ANTHROPIC_API_KEY (INV-003)
□ 2.  MCP через --mcp-config (временный JSON файл)
□ 3.  Никаких npm install — только встроенные Node.js модули (INV-001)
□ 4.  Три файла: server.js, index.html, agents_registry.json (INV-002)
□ 5.  Секции server.js: STORAGE / AGENT RUNNER / PLAN EXECUTOR /
       SMART ONBOARDING / API ENDPOINTS / SSE
□ 6.  ES modules: <script type="module">
□ 7.  Windows: path.join(), shell:true
□ 8.  generateTaskId() везде (INV-006)
□ 9.  Единый spawnClaude() (INV-008)
□ 10. Единый handleStderr() → bool isAuth
□ 11. Единый broadcastAuthError() из обоих режимов
□ 12. runAgentStream (UI чат) + runAgentSync (план, онбординг)
□ 13. buildPrompt null-safe (projectId=null → os.homedir(), пустые строки)
□ 14. buildMcpConfig: mcpServers=возможные, *Enabled=активирует
□ 15. Все STORAGE функции реализованы
□ 16. POST /agents реализован (найм/увольнение)
□ 17. extractJson() — общая функция для плана И онбординга (INV-007)
□ 18. extractPlan(text, userMessage) использует extractJson + fallback по тексту
□ 19. analyzeOnboarding() — параллельные Promise.allSettled (Boss + Setup)
□ 20. finalizeOnboarding() — сохраняет company, agents, memory, project, state
□ 21. POST /onboarding/analyze → немедленный ответ, результат через SSE
□ 22. POST /onboarding/finalize → синхронный ответ с projectId
□ 23. Fallback: если анализ провалился → onboarding_error {fallback:true} → ручной
□ 24. Обязательные агенты (boss, setup) всегда в финальном списке (INV-004)
□ 25. Кастомные агенты от системы показываются на подтверждение пользователю
□ 26. Ручной онбординг сохранён как альтернативный путь
□ 27. Все 11 агентов: gmailEnabled:false, fetchEnabled:false, browserEnabled:false
□ 28. tasks/index.json обновляется при каждом saveTask
□ 29. Статусы задачи: draft→plan_pending→plan_running→done→failed
□ 30. Список агентов везде: boss,backend,frontend,devops,integrator,
       analyst,sales,qa,smm,lawyer,setup
```

---

# РАЗДЕЛ 13. LIVING SPEC

```
□ ТЗ обновляется в той же итерации что и код
□ Изменение в коде без обновления ТЗ = технический долг
□ ADR и Decision Log обновляются при архитектурных решениях
□ Инварианты — только Артём может изменить (не агент самостоятельно)
□ При нарушении инварианта — агент останавливается и сообщает
```

---

# РАЗДЕЛ 14. AGENT REVIEW — ОБЯЗАТЕЛЬНО ПЕРЕД КОДОМ

*Агент выполняет этот раздел ДО начала написания кода.*

```
ПРОМПТ ДЛЯ АГЕНТА:
"Прочитай это ТЗ и найди:
1. Противоречия между разделами
2. Ситуации не описанные но которые точно возникнут
3. Edge cases отсутствующие в спецификации
4. Acceptance Criteria которых не хватает
5. Конфликты с инвариантами из Раздела 2.5
6. Forbidden Patterns которые могут быть нарушены

НЕ предлагай решения — только перечисляй проблемы.
Если нашёл > 3 проблем — остановись и жди ответов."
```

**Результат Agent Review:**
*(заполняется агентом после прочтения ТЗ перед написанием кода)*

Найденные проблемы:
1. ...
2. ...

Вопросы перед стартом:
1. ...

---

# ЧЕКЛИСТ ГОТОВНОСТИ ТЗ

```
ОБЯЗАТЕЛЬНО
□ Инварианты написаны (8 инвариантов — Раздел 2.5) ✅
□ Архитектурные принципы выбраны (8 принципов — Раздел 2.6) ✅
□ Что НЕ входит в scope — написано явно (Разделы 1.1, 9.3) ✅
□ Каждый ключевой модуль имеет Acceptance Criteria ✅
□ Acceptance Criteria конкретные и проверяемые ✅
□ Edge cases описаны для онбординга и чата ✅
□ API Contracts включают все endpoints ✅
□ Технологический стек зафиксирован (Раздел 2.4) ✅
□ Forbidden Patterns написаны (Раздел 11.1) ✅

КАЧЕСТВО
□ Нет противоречий между разделами ✅
□ Все термины в глоссарии (Раздел 2.1) ✅
□ Чеклист 30 требований включён (Раздел 12) ✅
□ Agent Review шаблон готов (Раздел 14) ✅
□ Decision Log заполнен (Раздел 5.5) ✅
```

---

*ANSS-Core v1.0 — Solo Founder OS — Артём Холомянский — 2026-06-01*
*Переработано из SOLO_FOUNDER_OS_ТЗ_v2.5.md по стандарту ANSS Standard v1.3*
