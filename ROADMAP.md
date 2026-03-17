# Claude Certified Architect – Foundations

## Повний Roadmap Підготовки

**Мета:** Досяжня мінімум 720 балів (pass/fail)
**Рекомендуваний час:** 120–150 годин активної практики
**Формат екзамену:** 4 сценарії з 6 можливих (множинний вибір)

---

## DOMAIN 1: AGENTIC ARCHITECTURE & ORCHESTRATION (27%)

### Тема 1.1: Agentic Loop Lifecycle (15 годин)

**Концепції для вивчення:**

- ✅ Як працює цикл запит-відповідь: `messages` → Claude → `stop_reason` → `tool_use` vs `end_turn`
- ✅ Структура відповіді Claude: `content` array, `tool_use` blocks, `text` blocks
- ✅ Вбудовування результатів tools у conversation history
- ✅ Коли цикл продовжується (stop_reason = "tool_use")
- ✅ Коли цикл завершується (stop_reason = "end_turn")
- ✅ Anti-patterns: парсинг природної мови для закінчення, arbitrary iteration caps

**Питання для самоперевірки:**

1. Напиши pseudocode простого agentic loop, який продовжує, поки `stop_reason == "tool_use"`, та зупиняється на `end_turn`
2. Чому перевірка на наявність text content **не є** надійним способом визначити завершення цикла?
3. Як додати результати tool-call у conversation context для наступної ітерації?
4. Яка різниця між `stop_reason: "tool_use"` та `stop_reason: "end_turn"`?
5. Напиши обробник помилок для tool call, який приймає результат або error і продовжує цикл

**Практичні завдання:**

- Реалізуй базовий agentic loop на Python або JavaScript, який:
  - Визначає 2–3 простих tools (калькулятор, пошук, умовна логіка)
  - Обробляє `stop_reason` правильно
  - Додає tool results у conversation history
  - Виводить вкінці фінальну відповідь
  - Запусти з 5+ різних запитів та переконайся, що цикл працює коректно
- Заведи лог всіх iterations для кожного запиту та проаналізуй, скільки кроків потрібно

---

### Тема 1.2: Multi-Agent Orchestration – Hub-and-Spoke (20 годин)

**Концепції для вивчення:**

- ✅ Hub-and-spoke архітектура: coordinator + subagents
- ✅ Coordinator як центральна точка управління, error handling, routing
- ✅ Subagents мають **ізольований контекст** — не наслідують conversation history coordinator
- ✅ Task decomposition: як coordinator вирішує, які subagents викликати
- ✅ Риски надто вузького decomposition (неповне покриття теми)
- ✅ Iterative refinement: coordinator оцінює synthesis, re-delegates, re-invokes
- ✅ Всесередня комунікація через coordinator (для observability)

**Питання для самоперевірки:**

1. Чому subagent **не** може автоматично наслідувати контекст coordinator? Як передати контекст явно?
2. Як coordinator визначає, які subagents запустити на основі запиту?
3. Напиши псевдокод для coordinator, який розбиває запит "дослідити вплив AI на креативні індустрії" на 5 підтем, розподіляє їх subagents й агрегує результати
4. Чому всесередня комунікація через coordinator більш надійна, ніж прямий контакт subagents?
5. Як coordinator визначить, коли synthesis недостатня, та як re-invoke субагентів з цільовими запитами?

**Практичні завдання:**

- Побудуй multi-agent research system:
  - Coordinator agent аналізує запит, планує 3 субagent завдання
  - Search subagent знаходить веб-результати (mock/real)
  - Analysis subagent резюмує документи
  - Synthesis subagent комбінує результати й виявляє пробіли
  - Coordinator запускає subagents паралельно (множинні Task calls в одній відповіді)
  - Coordinator оцінює синтез, визначає пробіли, re-delegates якщо потрібно
  - Тестуй на 3 складних дослідницьких темах; переконайся, що охоплення повне

---

### Тема 1.3: Subagent Invocation, Context Passing, Spawning (18 годин)

**Концепції для вивчення:**

- ✅ Task tool: механізм для запуску subagents
- ✅ `allowedTools: ["Task"]` — вимога для coordinator
- ✅ AgentDefinition: descriptions, system prompts, tool restrictions
- ✅ Контекст **явно** передається в prompt subagent, не наслідується
- ✅ Паралельне запуск: множинні Task calls в одній координаторній відповіді
- ✅ Структуровані формати для розділення контенту й метаданих (URL, імена документів, номери сторінок)
- ✅ Fork-based sessions для дослідження розбіжних підходів

**Питання для самоперевірки:**

1. Як написати AgentDefinition для search subagent? Які поля є, які описи потрібні?
2. Якщо coordinator знайшов 5 документів, як краще структурувати їх для transmission до synthesis subagent?
3. Напиши prompt для subagent, який отримує дані й явно повинен включити контекст попередніх знахідок
4. Як запустити 3 subagents паралельно в одній coordinator відповіді? Які дані повернути?
5. Як fork_session допомагає в дослідженні розбіжних підходів? Коли його використовувати?

**Практичні завдання:**

- Реалізуй систему, де coordinator:
  - Визначає 2–3 AgentDefinition структури (search, analysis, synthesis)
  - Для кожного вказує system prompt, description, allowedTools
  - Запускає subagents паралельно через Task (не послідовно)
  - Передає контекст явно (знахідки попередніх subagents у prompt)
  - Структурує metadata окремо від контенту (claim + source URL + publication date)
- Виміри часу паралельного vs послідовного запуску; переконайся, що паралельний швидший
- Протестуй із 10+ різними запитами дослідження

---

### Тема 1.4: Multi-Step Workflows – Enforcement & Handoff (16 годин)

**Концепції для вивчення:**

- ✅ Програмна enforcement (hooks, prerequisite gates) vs prompt-based guidance
- ✅ Коли deterministic compliance критична (фінансові операції, identity verification)
- ✅ Prompt instructions **не гарантують** compliance (non-zero failure rate)
- ✅ Structured handoff protocols: customer details, root cause, recommended actions
- ✅ Decomposition multi-concern запитів на окремі items для паралельної обробки
- ✅ Shared context між items перед синтезом unified resolution

**Питання для самоперевірки:**

1. Коли **тільки** hooks/enforcement варто використовувати замість prompt-based guidance?
2. Напиши pseudocode hook'у, який блокує `process_refund` поки `get_customer` не повернув verified customer ID
3. Як розбити customer запит типу "Я хочу повернути замовлення, оновити адресу та скарга на якість" на кроки?
4. Які дані мають бути включені в structured handoff summary коли escalate до human?
5. Як паралельно обробити 3 items у customer запиті зі shared context?

**Практичні завдання:**

- Побудуй customer support agent з enforcement:
  - Визнач 3 tools: `get_customer`, `lookup_order`, `process_refund`
  - Імплементуй hook, який блокує `process_refund` якщо customer ID не verified
  - Приймай запити типу "return item + update address + complaint"
  - Розбий на 3 паралельні tasks, виконай кожен, потім синтезуй
  - Коли필요 escalate, структуруй handoff з customer ID, root cause, amount, recommended action
  - Тестуй з 15+ різними customer запитами (прості, складні, що вимагають escalation)

---

### Тема 1.5: Agent SDK Hooks – Tool Call Interception (14 годин)

**Концепції для вивчення:**

- ✅ PostToolUse hook для трансформації результатів перед обробкою моделлю
- ✅ Tool call interception для блокування non-compliant операцій
- ✅ Hooks for deterministic guarantees vs prompt-based probabilistic compliance
- ✅ Data normalization: Unix timestamps → ISO 8601, status codes → labels
- ✅ Policy enforcement: блокування refunds > $500, redirect to escalation
- ✅ Hook chain: можна кілька hooks для різних concerns

**Питання для самоперевірки:**

1. Напиши PostToolUse hook, який трансформує Unix timestamp у ISO 8601 перед обробкою моделлю
2. Як написати tool call interception hook, який блокує `process_refund` з `amount > 500`?
3. Яка різниця в гарантіях: hook-based vs prompt-based enforcement для policy?
4. Як redirect користувача до alternative workflow (e.g., escalation) при блокуванні?
5. Якщо є 3 різні normalization потреби, чи писати 1 hook чи 3? Чому?

**Практичні завдання:**

- Реалізуй agent з 2–3 hooks:
  - PostToolUse hook для normalization (timestamps, status codes, formats)
  - Tool call interception hook для policy enforcement ($500 limit на refund)
  - Hook для logging всіх блокованих операцій
- Протестуй з tool results у різних форматах; переконайся, що normalization працює
- Спроба порушити policy (refund > 500); переконайся, що hook блокує й redirect до escalation
- Документуй поведінку hooks для кожного сценарію

---

### Тема 1.6: Task Decomposition Strategies (16 годин)

**Концепції для вивчення:**

- ✅ Fixed sequential pipelines (prompt chaining) vs dynamic adaptive decomposition
- ✅ Коли 使用 fixed: predictable multi-aspect reviews
- ✅ Коли 使用 adaptive: open-ended investigation tasks
- ✅ Prompt chaining: analyze per-file, then cross-file integration pass
- ✅ Large code reviews: split до per-file local + cross-file integration, не attention dilution
- ✅ Open-ended mapping: найду структуру → identify high-impact areas → prioritized plan

**Питання для самоперевірки:**

1. Коли fixed pipeline краще за adaptive decomposition? Наведи 2 приклади
2. Напиши pseudocode для code review, де спочатку per-file analysis, потім cross-file
3. Як 避免 attention dilution у large reviews (45+ files)?
4. Для задачі "добавити comprehensive тесты до legacy codebase", як спроектуй adaptive plan?
5. Як intermediate findings впливають на наступні tasks в adaptive decomposition?

**Практичні завдання:**

- Реалізуй 2 systems:
  1. **Fixed pipeline** для code review:
     - Stage 1: per-file local issues (bugs, style, naming)
     - Stage 2: cross-file integration (data flow, dependencies, API consistency)
     - Тестуй на 3 multi-file PRs (5–10 files)
  2. **Adaptive decomposition** для codebase analysis:
     - Спочатку map структуру, знайди high-impact areas
     - На основі findings, генеруй prioritized plan для наступних tasks
     - Таски адаптуються залежно від попередніх результатів
     - Тестуй на legacy codebases різних розмірів
- Порівняй attention quality, coverage, час обробки між двома підходами

---

### Тема 1.7: Session State, Resumption, Forking (12 годин)

**Концепції для вивчення:**

- ✅ Named session resumption: `--resume <session-name>` для продовження prior conversation
- ✅ `fork_session` для création independent branches від shared baseline
- ✅ Важливість інформування agent про file changes при resumption
- ✅ Коли resumption більш надійна vs start fresh з injected summaries
- ✅ Stale tool results при resumption: як оновити контекст

**Питання для самоперевірки:**

1. Як точно використовувати `--resume <session-name>` в Claude Code CLI?
2. Які сценарії потребують fork_session? Наведи 2–3 приклади
3. Якщо ти резюмив session після того як розробник змінив файли, як інформувати agent?
4. Коли resumption more reliable ніж start fresh + injected summary?
5. Як перевірити, що prior context все ще valid перед resumption?

**Практичні завдання:**

- Сценарій 1: Session resumption для long investigation
  - Запусти дослідження codebase (search, read, trace flows)
  - Збереж session с named name
  - Закрий і зовсім через час resume `--resume investigation-v1`
  - Повідом agent про specific файли що змінилися
  - Продовжи дослідження з prior context
  - Переконайся, що prior findings все ще використовуються
- Сценарій 2: fork_session для дослідження розбіжних підходів
  - Аналізуй codebase и знайди high-impact area
  - fork_session: підхід A (refactoring strategy 1)
  - fork_session: підхід B (refactoring strategy 2)
  - Порівняй результати обох branches

---

## DOMAIN 2: TOOL DESIGN & MCP INTEGRATION (18%)

### Тема 2.1: Effective Tool Interfaces & Descriptions (16 годин)

**Концепції для вивчення:**

- ✅ Tool descriptions як primary mechanism для LLM tool selection
- ✅ Мінімальні descriptions → unreliable selection серед similar tools
- ✅ Що включити в descriptions: input formats, example queries, edge cases, boundaries
- ✅ Ambiguous descriptions → misrouting (analyze_content vs analyze_document)
- ✅ System prompt wording впливає на tool selection (keyword-sensitive)
- ✅ Renaming + updating descriptions для elimination functional overlap
- ✅ Splitting generic tools → purpose-specific з defined input/output contracts

**Питання для самоперевірки:**

1. Напиши description для tool, який явно differentiates його від similar tool
2. Коли tool description insufficient, чому few-shot examples недостатні як first fix?
3. Як перевірити tool descriptions на keyword-sensitive system prompt interference?
4. Приклад: у тебе `analyze_content` і `analyze_document` з nearly identical descriptions. Як переніміж і перепиши?
5. Як розбити generic `analyze_tool` на 3–4 purpose-specific tools із clear contracts?

**Практичні завдання:**

- Дизайн tool suite для document processing:
  - Почни з 5 generic tools, з minimal descriptions
  - Побудуй agent запитати для взаємодії з ними; спостерігай misrouting
  - Перепиши descriptions: додай input formats, example queries, edge cases, clear boundaries
  - Rename tools для clarity (e.g., `analyze_content` → `extract_web_results`)
  - Re-test с тими ж запитами; переконайся, що misrouting зменшився
  - Доку всі iterations і lessons learned

---

### Тема 2.2: Structured Error Responses in MCP (14 годин)

**Концепції для вивчення:**

- ✅ MCP `isError` flag для communicating tool failures
- ✅ Distinct error types: transient (timeout), validation (bad input), business (policy), permission
- ✅ Uniform error responses → agent не може recover intelligently
- ✅ Retryable vs non-retryable errors + metadata
- ✅ Local error recovery в subagents перед escalation до coordinator
- ✅ Розрізнення: access failures (retry) vs valid empty results (success)

**Питання для самоперевірки:**

1. Напиши JSON структуру для structured error response з `errorCategory`, `isRetryable`, message
2. Які 4 error types потрібно розрізнювати? Приклади для кожного
3. Як agent повинен обробляти error з `isRetryable: false`?
4. Для timeout в web search subagent: як сформулювати error щоб coordinator міг decide retry?
5. Як розрізнити "search returned 0 results" (success) від "search service unavailable" (error)?

**Практичні завдання:**

- Реалізуй MCP tools з structured error handling:
  - Tool 1: make external API call (mock transient failures, validation errors)
  - Tool 2: database query (mock permission errors, business logic violations)
  - Кожен error повертає structured metadata з category, retryable flag, explanation
  - Побудуй agent, який:
    - Логує error type
    - Retry transient errors з exponential backoff
    - Explain business errors до user без retry
    - Handle permission errors з escalation suggestion
  - Протестуй з 20+ error scenarios; переконайся, що agent recovery logical

---

### Тема 2.3: Tool Distribution & tool_choice Config (14 годин)

**Концепції для вивчення:**

- ✅ Too many tools → degraded selection reliability (18 tools vs 4–5)
- ✅ Agents з tools outside specialization → misuse (synthesis agent web searches)
- ✅ Scoped tool access: give agent лише tools для її role, limited cross-role tools
- ✅ `tool_choice: "auto"` (model може return text замість calling tool)
- ✅ `tool_choice: "any"` (model must call tool, але can choose which)
- ✅ Forced tool selection: `{"type": "tool", "name": "..."}`

**Питання для самоперевірки:**

1. Яке optimal кількість tools на agent? Як ти обгрунтуєш limit?
2. Напиши tool distribution для 3-subagent research system: які tools кожен отримує?
3. Когда використовувати `tool_choice: "any"` instead of "auto"?
4. Як використовувати forced tool selection для гарантування specific tool першим?
5. Приклад: synthesis agent неправильно uses web search tool. Як fix?

**Практичні завдання:**

- Реалізуй multi-agent system з tool scoping:
  - Search agent: 2–3 search tools, 1 verify-fact tool
  - Analysis agent: 4–5 extraction/analysis tools
  - Synthesis agent: 1–2 formatting tools, 1 verify-fact tool
  - Coordinator: Task tool, no domain-specific tools
  - Test tool selection reliability для кожного agent
  - Observ misuse cases; adjust tool_choice configs або remove cross-role tools
  - Measure: success rate tool selection, false usage rate

---

### Тема 2.4: MCP Server Integration into Claude Code & Agents (15 годин)

**Концепції для вивчення:**

- ✅ MCP server scoping: project-level (.mcp.json) vs user-level (~/.claude.json)
- ✅ Environment variable expansion: `${GITHUB_TOKEN}` для credentials без committing
- ✅ Tools від всіх configured MCP servers discoverable одночасно
- ✅ MCP resources: expose content catalogs (issues, docs, schemas) для reduce exploratory calls
- ✅ Community vs custom MCP servers: choose community для standard integrations
- ✅ Tool descriptions для MCP: explain capabilities, prevent preferring built-in tools

**Питання для самоперевірки:**

1. Яка різниця між .mcp.json (project) vs ~/.claude.json (user)?
2. Як настроїти environment variable expansion для credentials у .mcp.json?
3. Як expose content catalog як MCP resource? Приклади resource types
4. Коли писати custom MCP server vs use existing community server?
5. Як enhance MCP tool descriptions щоб agent preferred їх over built-in tools?

**Практичні завдання:**

- Налаштуй MCP servers для team project:
  - Create project .mcp.json з 2 MCP servers (e.g., GitHub, Jira), env var expansion
  - Create user ~/.claude.json з 1 personal experimental server
  - Verify both servers available одночасно в Claude Code session
  - Create MCP resource catalog (e.g., GitHub issues list, Jira projects)
  - Test: agent може обирати MCP tools vs built-in tools
  - Enhance MCP tool descriptions; re-test selection reliability
  - Документуй configuration, available tools, resources для team

---

### Тема 2.5: Built-in Tools – Read, Write, Edit, Bash, Grep, Glob (14 годин)

**Концепції для вивчення:**

- ✅ Grep: пошук контенту (function names, error messages, imports)
- ✅ Glob: pattern matching по file paths (find files by name/extension)
- ✅ Read/Write: full file operations; Edit: targeted modifications
- ✅ Edit failure (non-unique matches) → fallback to Read + Write
- ✅ Building codebase understanding incrementally: Grep → Read → trace flows
- ✅ Bash: execute shell commands, pipelines
- ✅ Selecting right tool для right task

**Питання для самоперевірки:**

1. Коли обираєш Grep vs Glob? Наведи приклади для кожного
2. Напиши Glob pattern для найти всі test files: \*_/_.test.tsx
3. Коли Edit хороший vs коли Read + Write потрібен?
4. Як trace function usage через codebase: алгоритм з Grep + Read?
5. Bash command для найти всі Python files що імпортують `tensorflow`?

**Практичні завдання:**

- Codebase analysis task:
  - Дано: large Python project (500+ files)
  - Task: знайти всі callers функції `process_payment`, trace до entry points
  - Використовуй: Glob → find .py files, Grep → find `process_payment`, Read → trace imports
  - Task: знайти всі error messages содержать "timeout" и контекст де вони thrown
  - Використовуй: Bash grep pipeline → Grep tool → Read для context
  - Task: update 20+ files заміняючи `old_config` на `new_config` (unique matches)
  - Спроба Edit; де неуспішно, використовуй Read + Write
  - Документуй всі steps и ratio Edit vs Read+Write successes

---

## DOMAIN 3: CLAUDE CODE CONFIGURATION & WORKFLOWS (20%)

### Тема 3.1: CLAUDE.md Hierarchy & Configuration (14 годин)

**Концепції для вивчення:**

- ✅ Hierarchy: user-level (~/.claude/CLAUDE.md), project-level (.claude/CLAUDE.md or root), directory-level
- ✅ User-level settings **not shared** via version control
- ✅ `@import` syntax для referencing external files (keep modular)
- ✅ `.claude/rules/` directory для topic-specific rule files
- ✅ Rule file precedence и override behavior
- ✅ `/memory` command для verify які memory files loaded

**Питання для самоперевірки:**

1. Напиши project-level CLAUDE.md з universal coding standards та testing conventions
2. Як використовувати @import для selective inclusion різних standards files?
3. Яка різниця між monolithic CLAUDE.md vs .claude/rules/ modular approach?
4. Як діагностувати configuration hierarchy issues (e.g., new team member не отримав instructions)?
5. Напиши 3 path-specific rule files для React, API, database areas

**Практичні завдання:**

- Настрій CLAUDE.md hierarchy для team project:
  - Create user-level: ~/.claude/CLAUDE.md з personal preferences (не share!)
  - Create project root: ./CLAUDE.md з universal standards
  - Create directory-level: ./src/react/CLAUDE.md з React-specific rules
  - Create .claude/rules/: 5 topic-specific rule files (testing.md, api.md, db.md, security.md, performance.md)
  - Use @import у project CLAUDE.md для import relevant rules
  - Test з новим team member: verify вони not get user-level rules, але get project + directory rules
  - Використовуй `/memory` команду для verify loaded files
  - Документуй hierarchy і test scenarios

---

### Тема 3.2: Custom Slash Commands & Skills (16 годин)

**Концепції для вивчення:**

- ✅ Project-scoped commands: .claude/commands/ (shared via git)
- ✅ User-scoped commands: ~/.claude/commands/ (personal)
- ✅ Skills у .claude/skills/ з SKILL.md files та frontmatter
- ✅ Frontmatter options: `context: fork`, `allowed-tools`, `argument-hint`
- ✅ `context: fork` для isolated sub-agent (prevent output pollution)
- ✅ Personal skill customization з different names
- ✅ Коли skills vs CLAUDE.md: skill=on-demand, CLAUDE.md=always-loaded

**Питання для самоперевірки:**

1. Напиши project-scoped `/review` command для standard code review checklist
2. Як створити skill з `context: fork` для verbose codebase analysis?
3. Напиши frontmatter для skill: `allowed-tools: ["Read", "Grep"]` (запобігти destructive actions)
4. Коли обираєш skill vs CLAUDE.md instructions?
5. Як personnalize skill у ~/.claude/skills/ different name щоб не affect teammates?

**Практичні завдання:**

- Реалізуй command + skill system для team:
  - Create project command: `.claude/commands/review.md` — standard code review checklist
  - Create project command: `.claude/commands/test.md` — test generation guidelines
  - Create project skill: `.claude/skills/analyze_codebase/SKILL.md` з `context: fork`, `allowed-tools: ["Read", "Grep", "Bash"]`
  - Create project skill: `.claude/skills/generate_docs/SKILL.md` з `allowed-tools: ["Write", "Read"]`
  - Test: invoke commands та skills; verify fork context isolation
  - Personnalize одну skill у ~/.claude/skills/ different name; verify не affect teammates
  - Документуй usage patterns и when developers should invoke which

---

### Тема 3.3: Path-Specific Rules (14 годин)

**Концепції для вивчення:**

- ✅ `.claude/rules/` files з YAML frontmatter `paths` glob patterns
- ✅ Path-scoped rules load **only** when editing matching files
- ✅ Reduce irrelevant context, token usage
- ✅ Glob patterns для conventions spanning multiple directories
- ✅ Rules для test files spread throughout (e.g., \*_/_.test.tsx)
- ✅ Choosing path-specific rules vs directory-level CLAUDE.md

**Питання для самоперевірки:**

1. Напиши path-specific rule файл для всіх Terraform files: paths: ["terraform/**/*"]
2. Як використовувати glob patterns для test files regardless directory location?
3. Коли path-specific rules better ніж directory-level CLAUDE.md?
4. Напиши YAML frontmatter для 3 різні path scopes в одному file
5. Як перевірити, що path-specific rule loaded only для matching files?

**Практичні завдання:**

- Реалізуй path-specific rules для heterogeneous codebase:
  - Create .claude/rules/react.md: paths: ["src/**/*.tsx", "src/**/*.jsx"]
  - Create .claude/rules/api.md: paths: ["api/**/*.py", "handlers/**/*.py"]
  - Create .claude/rules/tests.md: paths: ["**/*.test.*", "**/*.spec.*"]
  - Create .claude/rules/terraform.md: paths: ["terraform/**/*"]
  - Test: edit React file → verify react.md loaded, others not
  - Test: edit Python API file → verify api.md loaded
  - Test: edit test file → verify tests.md loaded (regardless directory)
  - Measure token usage: path-scoped vs monolithic CLAUDE.md
  - Документуй patterns и benefits

---

### Тема 3.4: Plan Mode vs Direct Execution (14 годин)

**Концепції для вивчення:**

- ✅ Plan mode: complex tasks, large-scale changes, multiple approaches, architecture decisions
- ✅ Direct execution: simple, well-scoped changes (single file bug fix, clear stack trace)
- ✅ Plan mode: safe codebase exploration + design перед committing
- ✅ Prevents costly rework
- ✅ Explore subagent: isolate verbose discovery, return summaries
- ✅ Combining: plan mode investigation + direct execution implementation

**Питання для самоперевірки:**

1. Коли обираєш plan mode? Наведи 3–4 criteria
2. Коли обираєш direct execution? Приклади
3. Як використовувати Explore subagent для preserve main conversation context?
4. Workflow: plan migration (45+ files) → direct execute planned approach. Як структурувати?
5. Коли plan mode overhead не justified vs direct execution?

**Практичні завдання:**

- Протестуй обидва modes на різних задачах:
  1. Direct execution:
     - Single-file bug fix з clear stack trace
     - Simple feature addition (1–2 files)
     - Measure: time, success rate, rework iterations
  2. Plan mode:
     - Microservice restructuring (45+ files)
     - Library migration з multiple valid approaches
     - Measure: planning time, execution time, total iterations
  3. Hybrid:
     - Complex investigation (plan mode) → implementation (direct execution)
     - Measure: total efficiency vs plan-only vs direct-only
  - Документуй які metrics drove decisions

---

### Тема 3.5: Iterative Refinement Techniques (14 годин)

**Концепції для вивчення:**

- ✅ Concrete input/output examples > prose descriptions
- ✅ Test-driven iteration: write tests first, iterate by sharing failures
- ✅ Interview pattern: Claude asks questions перед implementing
- ✅ Single message (interacting problems) vs sequential (independent)
- ✅ Progressive improvement через specific test cases

**Питання для самоперевірки:**

1. Напиши 3 concrete input/output examples для transformation requirement (не prose)
2. Як структурувати test-driven iteration: коли share test failures?
3. Коли обираєш interview pattern перед implementation?
4. Як distinguish interacting vs independent issues для decide single vs sequential fix?
5. Приклад: edge case handling у migration script. Як iterate?

**Практичні завдання:**

- Реалізуй iterative refinement workflows:
  1. Input/output examples:
     - Task: transform messy JSON → clean CSV
     - Напиши 3 I/O examples замість prose description
     - Iteratively refine доки найкращий output
  2. Test-driven:
     - Task: implement payment processor feature
     - Write comprehensive test suite (happy path, edge cases, errors)
     - Share test failures с Claude; iterate
     - Count iterations до green tests
  3. Interview pattern:
     - Task: add caching layer до legacy system
     - Claude asks questions: cache invalidation strategy? Failure modes? Dependencies?
     - Document questions + answers, then implement
  4. Interacting problems:
     - Task: fix 5 bugs з 3 interacting, 2 independent
     - Provide all interacting в одній message
     - Provide independent separately
     - Compare iterations efficiency

---

### Тема 3.6: Claude Code in CI/CD Pipelines (14 годин)

**Концепції для вивчення:**

- ✅ `-p` (or `--print`) flag для non-interactive mode
- ✅ `--output-format json` and `--json-schema` для structured output
- ✅ CLAUDE.md як mechanism для provide project context до CI-invoked Claude Code
- ✅ Session context isolation: generator vs independent reviewer effectiveness
- ✅ Include prior review findings для avoid duplicate comments
- ✅ Existing test files у context для avoid duplicate test scenarios
- ✅ Document testing standards, fixtures у CLAUDE.md

**Питання для самоперевірки:**

1. Напиши CI script, що runs Claude Code з `-p` flag для automated code review
2. Як use `--output-format json --json-schema` для PR comments?
3. Як include prior review findings для next run (avoid duplicates)?
4. Коли independent review instance better ніж self-review?
5. Як provide test fixtures у CLAUDE.md для improve test generation?

**Практичні завдання:**

- Настрій Claude Code у CI/CD pipeline:
  - GitHub Actions workflow (or similar) z Claude Code step
  - `-p` flag для non-interactive, `--output-format json`
  - `--json-schema` для structured findings
  - CLAUDE.md з testing standards, review criteria, valuable test types
  - Run на 5+ real PRs; capture findings JSON
  - Parse JSON → post inline PR comments
  - Iterate на 3 PRs з high false positives; disable problematic categories
  - Second independent instance для review same PRs; compare findings
  - Документуй CI setup, prompt tuning, false positive reduction

---

## DOMAIN 4: PROMPT ENGINEERING & STRUCTURED OUTPUT (20%)

### Тема 4.1: Explicit Criteria for Precision (14 годин)

**Концепції для вивчення:**

- ✅ Explicit criteria > vague instructions ("flag only when claimed ≠ actual" vs "check accuracy")
- ✅ General instructions ("be conservative") не improve precision
- ✅ False positive rate впливає на developer trust
- ✅ High false positives → disable category, improve prompt
- ✅ Severity criteria з concrete code examples
- ✅ Categorical criteria (bugs, security vs style, local patterns)

**Питання для самоперевірки:**

1. Напиши explicit review criteria для code review tool (5–6 categories)
2. Як contrast "be conservative" (vague) vs explicit criteria (precise)?
3. Коли disabled high false positive category, як rotate у prompt improvement?
4. Напиши severity criteria з concrete code examples: Low, Medium, High, Critical
5. Як measure false positive rate per category?

**Практичні завдання:**

- Реалізуй iterative prompt refinement для code review:
  - v1: vague criteria ("check for bugs, security issues, style")
  - Test на 10 PRs; measure false positive rate per category
  - Identify high false positive category (e.g., "style issues")
  - v2: explicit criteria для всіх categories; disable style issues
  - Re-test на same 10 PRs; measure improvement
  - v3: add concrete code examples для severity levels
  - Re-test; measure precision per severity
  - Документуй evolution, false positive rates, final prompt

---

### Тема 4.2: Few-Shot Prompting (14 годин)

**Концепції для вивчення:**

- ✅ Few-shot > detailed instructions alone для consistency
- ✅ Ambiguous-case handling через examples (tool selection, coverage gaps)
- ✅ Model generalize judgment до novel patterns (не just memorize examples)
- ✅ Reduce hallucination in extraction (informal measurements, varied structures)
- ✅ 2–4 targeted examples per ambiguous scenario
- ✅ Include specific output format в examples

**Питання для самоперевірки:**

1. Напиши 3 few-shot examples для ambiguous code review scenarios
2. Як структурувати few-shot example для demonstrate reasoning?
3. Коли 2 examples достатньо vs 4–5 needed?
4. Приклад: extraction з varied document structures. Як design examples?
5. Як measure effectiveness: few-shot with detailed instructions + few-shot?

**Практичні завдання:**

- Few-shot prompt optimization:
  - Task 1: Extraction з varied document formats
    - v1: detailed instructions (no examples)
    - v2: add 2 few-shot examples
    - v3: add 4 examples covering edge cases
    - Test на 20 documents; measure accuracy per version
  - Task 2: Tool selection для ambiguous requests
    - v1: descriptions only
    - v2: add 3 few-shot examples showing reasoning
    - Test на 15 ambiguous requests; measure selection reliability
  - Документуй improvement curve: instructions vs 2-shot vs 4-shot

---

### Тема 4.3: Structured Output via Tool Use & JSON Schemas (16 годин)

**Концепції для вивчення:**

- ✅ Tool use + JSON schemas: most reliable для schema-compliant output
- ✅ Eliminates JSON syntax errors
- ✅ `tool_choice: "auto"` vs "any" vs forced
- ✅ Strict schemas eliminate syntax errors, не semantic errors
- ✅ Schema design: required vs optional, enum з "other" + detail string
- ✅ Nullable fields для prevent hallucination when info absent

**Питання для самоперевірки:**

1. Напиши extraction tool з JSON schema як input parameter
2. Коли обираєш `tool_choice: "any"` vs forced selection?
3. Як design schema для prevent hallucination (nullable fields)?
4. Приклад: schema з enum "status" + "other" + detail string. Як structure?
5. Semantic validation errors (line items ≠ total). Як detect?

**Практичні завдання:**

- Structured data extraction pipeline:
  - Define extraction tool з 5-field JSON schema
  - Include: required fields, optional fields, enum z "other" option
  - Test на 20 documents; measure syntax error rate vs text-based approach
  - Add semantic validation: "calculated_total" vs "stated_total" mismatch detection
  - Implement validation-retry loop: on error, resubmit з specific error + document
  - Measure retry success rate
  - Документуй schema design, validation logic, error handling

---

### Тема 4.4: Validation, Retry & Feedback Loops (14 годин)

**Концепції для вивчення:**

- ✅ Retry-with-error-feedback: append specific errors на retry
- ✅ Retry limitations: ineffective коли info просто absent
- ✅ Feedback loop design: track detected_pattern для analysis dismissals
- ✅ Semantic validation errors (don't sum, wrong field)
- ✅ Schema syntax errors eliminated by tool use

**Питання для самоперевірки:**

1. Напиши retry flow: provide document + failed extraction + specific error
2. Коли retry ineffective? Як detect?
3. Як use detected_pattern field для analyze false positives?
4. Design self-correction validation: calculated vs stated values, conflict detection
5. Як distinguish format errors (retry-able) vs missing info (not retry-able)?

**Практичні завдання:**

- Validation-retry loop system:
  - Extraction task з 10 fields (numerical, dates, categories)
  - Implement validations: format checks, sum checks, required field checks
  - On failure: retry з specific error message + original document
  - Track: attempt count, success rate, error categories
  - Add detected_pattern field; analyze developer dismissals
  - Measure: retry success rate, diminishing returns (when retry shouldn't rerun)
  - Документуй validation rules, retry strategy, success metrics

---

### Тема 4.5: Batch Processing Strategies (12 годин)

**Концепції для вивчення:**

- ✅ Message Batches API: 50% cost savings, 24-hour processing, no SLA
- ✅ Appropriate: overnight reports, weekly audits, non-blocking
- ✅ Not appropriate: pre-merge checks, blocking workflows
- ✅ No multi-turn tool calling within single batch request
- ✅ custom_id для correlate request/response pairs
- ✅ Batch submission frequency для SLA constraints

**Питання для самоперевірки:**

1. Коли Batch API appropriate? Приклади. Когда не?
2. Напиши batch submission frequency calculation для 30-hour SLA z 24-hour batch window
3. Як handle batch failure: resubmit by custom_id з modifications?
4. Коли prompt refinement на sample set перед batch?
5. Який cost savings: sync API vs Batch API? Context?

**Практичні завдання:**

- Batch processing pipeline:
  - Task: extract data з 1000 documents overnight
  - Design batch requests з custom_id correlation
  - Calculate submission frequency для 24-hour processing, 30-hour SLA
  - Implement failure handling: resubmit failed custom_ids
  - Compare cost: sync API (1000 calls) vs Batch API
  - Measure: total processing time, cost savings, reliability
  - Документуй batch design, SLA calculations, cost analysis

---

### Тема 4.6: Multi-Instance & Multi-Pass Review (14 годин)

**Концепції для вивчення:**

- ✅ Self-review limitations: model retains reasoning, less likely question own decisions
- ✅ Independent instance > self-review instructions
- ✅ Multi-pass: per-file local + cross-file integration для avoid attention dilution
- ✅ Verify confidence alongside findings
- ✅ Calibrated review routing

**Питання для самоперевірки:**

1. Чому independent review instance more effective ніж self-review?
2. Напиши multi-pass review architecture для 45-file PR
3. Як use confidence scoring для route findings to human review?
4. Коли single-pass review sufficient vs requiring multi-pass?
5. Як structure per-file vs cross-file passes?

**Практичні завдання:**

- Multi-pass review system:
  - Large PR: 45 files (React, API, DB components)
  - Pass 1: independent instance, per-file local issues
  - Pass 2: independent instance, cross-file integration issues
  - Pass 3: self-review of generated findings (confidence calibration)
  - Compare Pass 1 vs single-pass approach (same model, all files together)
  - Measure: depth per file, cross-file consistency, missed issues
  - Confidence calibration: output confidence 1-10 per finding
  - Validate calibration на labeled test set; adjust thresholds
  - Route low-confidence до human review
  - Документуй architecture, comparative metrics, calibration

---

## DOMAIN 5: CONTEXT MANAGEMENT & RELIABILITY (15%)

### Тема 5.1: Long-Context Management (14 годин)

**Концепції для вивчення:**

- ✅ Progressive summarization risks: lose numerical values, dates, expectations
- ✅ "Lost in middle" effect: reliable at start/end, omit middle findings
- ✅ Tool results accumulate: 40+ fields per lookup, only 5 relevant
- ✅ Complete conversation history needed для coherence
- ✅ Persistent "case facts" block outside summarized history
- ✅ Structured issue data у separate context layer (multi-issue sessions)
- ✅ Trim verbose tool outputs before accumulation

**Питання для самоперевірки:**

1. Як extract transactional facts (amounts, dates, order numbers) в persistent block?
2. Коли progressive summarization problematic? Приклади
3. Як структурувати metadata (dates, sources, methodology) для downstream use?
4. Design context layout для multi-issue customer support session?
5. Як modify upstream agent для return structured (claims, citations) instead verbose?

**Практичні завдання:**

- Long-context optimization:
  - Customer support session: 5 customer issues з 50+ tool lookups
  - v1: default context (all history + all tool results)
  - v2: persistent "case facts" block (customer ID, order IDs, amounts, dates outside summary)
  - v3: trim tool results: keep only relevant fields
  - v4: structured issue data layer (separate from prose history)
  - Measure: context window usage, information retention accuracy, agent decision quality
  - Test: retrieve facts from middle of long context; measure accuracy across positions
  - Документуй optimization techniques, context savings, accuracy improvements

---

### Тема 5.2: Escalation & Ambiguity Resolution (14 годин)

**Концепції для вивчення:**

- ✅ Escalation triggers: explicit customer request, policy gaps, unable to progress
- ✅ Distinguish: immediate escalation vs offer to resolve (straightforward cases)
- ✅ Sentiment-based + self-reported confidence unreliable proxies
- ✅ Multiple matches: clarify (request identifiers), не heuristic selection
- ✅ Explicit escalation criteria + few-shot examples в system prompt

**Питання для самоперевірки:**

1. Напиши escalation criteria з few-shot examples для system prompt
2. Коли honor explicit customer request немедленно vs offer to resolve?
3. Приклад: multiple customer matches у database. Як handle?
4. Коли escalate на policy gap/silence на customer request?
5. Як acknowledge frustration + offer resolution vs immediate escalate?

**Практичні завдання:**

- Escalation calibration:
  - Define 5 escalation triggers z few-shot examples
  - System prompt z explicit criteria
  - Test на 30 customer support scenarios:
    - Straightforward cases (should resolve)
    - Complex cases (need escalation)
    - Ambiguous cases (customer requests human)
    - Policy gaps
    - Multiple matches scenarios
  - Measure: escalation precision (true positives vs false positives)
  - Iterate на high false positive scenarios
  - Документуй criteria, examples, test results, improvements

---

### Тема 5.3: Error Propagation in Multi-Agent (14 годин)

**Концепції для вивчення:**

- ✅ Structured error context: failure type, attempted query, partial results, alternatives
- ✅ Distinguish: access failures (retry-able) vs valid empty results (success)
- ✅ Subagent local recovery перед escalation до coordinator
- ✅ Generic error statuses hide coordinator options
- ✅ Synthesis output з coverage annotations: well-supported vs gaps

**Питання для самоперевірки:**

1. Напиши structured error context format для subagent failures
2. Як distinguish access failure від valid empty result?
3. Коли subagent should retry locally vs escalate to coordinator?
4. Приклад: web search timeout. Як propagate context для coordinator recovery options?
5. Як annotate synthesis output з coverage (well-supported vs gaps)?

**Практичні завдання:**

- Error propagation system:
  - Multi-agent research (search, analysis, synthesis)
  - Implement structured error responses від subagents
  - Coordinator receives errors: decide retry vs alternative approach vs proceed partial
  - Simulate: transient failures (timeouts), valid empty (no results), access failures
  - Measure: coordinator recovery success rate per error type
  - Coverage annotations: track which findings well-supported, which have gaps
  - Final synthesis: report gap areas clearly
  - Документуй error handling, coordinator logic, coverage tracking

---

### Тема 5.4: Long Codebase Exploration (14 годин)

**Концепції для вивчення:**

- ✅ Context degradation: inconsistent answers, "typical patterns" vs specific findings
- ✅ Scratchpad files: persist key findings across context boundaries
- ✅ Subagent delegation: isolate verbose output, main agent coordinates
- ✅ Structured state persistence: crash recovery manifests
- ✅ `/compact` command для reduce context usage

**Питання для самоперевірки:**

1. Як detect context degradation в extended session?
2. Як використовувати scratchpad files для cross-boundary persistence?
3. Як структурувати agent state для crash recovery?
4. Коли delegate to subagent vs direct exploration?
5. Як use `/compact` effectively?

**Практичні завдання:**

- Long exploration workflow:
  - Large codebase (1000+ files, complex dependencies)
  - Phase 1: map structure, identify high-impact areas (verbose output → subagent z fork_session)
  - Scratchpad file: record key findings
  - Phase 2: investigate specific area, reference scratchpad
  - Measure context degradation: consistency per iteration
  - Crash recovery: save agent state manifests, resume from checkpoint
  - Test: resume session после crash; verify prior findings not lost
  - Compare: direct exploration vs scratchpad vs subagent delegation efficiency
  - Документуй context management techniques, degradation metrics, recovery process

---

### Тема 5.5: Human Review Workflows & Confidence (14 годин)

**Концепції для вивчення:**

- ✅ Aggregate accuracy masking poor performance on specific types/fields
- ✅ Stratified random sampling для error rate measurement
- ✅ Field-level confidence scores calibrated на labeled validation set
- ✅ Accuracy verification by document type + field перед automation
- ✅ Route low-confidence або ambiguous 到 human review

**Питання для самоперевірки:**

1. Як use stratified sampling для detect error patterns?
2. Як calibrate field-level confidence на validation set?
3. Приклад: extraction accuracy 97% overall but 60% на specific field. Как handle?
4. Як decide review threshold для route confidence to human?
5. Як measure accuracy by document type + field?

**Практичні завдання:**

- Confidence calibration system:
  - Extraction task, 15 fields, 5 document types
  - Generate predictions з field-level confidence scores
  - Labeled validation set: 500 documents з ground truth
  - Calibrate: confidence percentiles vs actual accuracy
  - Analyze: accuracy by field, by document type
  - Identify poor-performing segments (e.g., field X on document type Y)
  - Set review thresholds: route low confidence to human
  - Measure: coverage, human review workload vs automation accuracy gains
  - Документуй calibration curve, decision thresholds, performance by segment

---

### Тема 5.6: Information Provenance & Multi-Source Synthesis (14 годин)

**Концепції для вивчення:**

- ✅ Source attribution lost during summarization (no claim-source mapping)
- ✅ Structured claim-source mappings: synthesis agent must preserve + merge
- ✅ Conflicting statistics: annotate with source, не arbitrary selection
- ✅ Temporal data: publication/collection dates в structured outputs
- ✅ Render different content types appropriately (tables, prose, structured lists)

**Питання для самоперевірки:**

1. Напиши structured claim-source mapping format
2. Як synthesis agent preserve attribution при combining findings?
3. Приклад: two credible sources, different statistics. Как structure output?
4. Чому publication dates critical? Приклади misinterpretation
5. Як distinguish financial data (table) vs news (prose) vs technical (list) rendering?

**Практичні завдання:**

- Multi-source synthesis system:
  - Research task: synthesis від 3 subagents (search, documents, interviews)
  - Subagents return: structured claim-source mappings з URL, date, excerpt
  - Synthesis agent: preserve attribution, merge findings, annotate conflicts
  - Conflicting data: don't choose one, annotate both з sources
  - Temporal handling: include dates, check for temporal misalignments
  - Render appropriately: financial (table), news (prose narrative), technical (bullet list)
  - Final report: distinguish well-established vs contested findings
  - Документуй data structures, synthesis logic, output rendering

---

## EXAM PREPARATION SUMMARY

### Recommended Study Sequence (120–150 hours)

**Week 1–2: Foundations (20 hours)**

- Domain 1.1 + 1.2 (Agentic Loop + Multi-Agent Basics)
- Domain 2.1 + 2.2 (Tool Design + Error Handling)
- Read full exam guide again; take notes on scenarios

**Week 3–4: Core Agent Patterns (25 hours)**

- Domain 1.3 + 1.4 + 1.5 (Subagent Invocation, Workflows, Hooks)
- Domain 1.6 + 1.7 (Decomposition, Session Management)
- Build 2–3 complete agentic systems from exercises

**Week 5: Tools & MCP (20 hours)**

- Domain 2.3 + 2.4 + 2.5 (Tool Distribution, MCP, Built-in Tools)
- Set up team MCP configuration (hands-on)

**Week 6–7: Claude Code & Configuration (20 hours)**

- Domain 3.1 → 3.6 (CLAUDE.md, Commands, Skills, Plan Mode, Workflows)
- Configure real project z CLAUDE.md hierarchy + rules + skills

**Week 8: Prompting & Structured Output (20 hours)**

- Domain 4.1 → 4.6 (Criteria, Few-Shot, JSON Schemas, Validation, Batch, Multi-Pass)
- Iterative refinement exercises

**Week 9: Context & Reliability (20 hours)**

- Domain 5.1 → 5.6 (Context Management, Escalation, Error Propagation, Provenance)
- Build end-to-end systems з all context patterns

**Week 10: Integration & Practice (15 hours)**

- Combine all domains: build 1 large multi-agent system incorporating:
  - Agent orchestration + tool design + MCP
  - Claude Code configuration
  - Structured output + prompt engineering
  - Context management + reliability
- Complete practice exam
- Review weak areas

---

## Practical Projects to Complete

### Project 1: Multi-Agent Research System

**Domains:** 1.1–1.7, 2.1–2.5, 5.3, 5.6

- Build coordinator + 3–4 subagents (search, analysis, synthesis, verification)
- Implement MCP tools, structured errors, context passing
- Multi-pass workflow с feedback loops
- Handle conflicting sources, maintain provenance
- Time budget: 20 hours

### Project 2: Claude Code Workflows for Team

**Domains:** 3.1–3.6, 2.4

- Configure CLAUDE.md hierarchy для team project
- Create .claude/rules/ з glob patterns
- Create project commands + personal skills
- Integrate MCP servers (GitHub, Jira, or custom)
- Set up plan mode vs direct execution workflows
- Time budget: 15 hours

### Project 3: Structured Data Extraction Pipeline

**Domains:** 4.1–4.5, 5.1, 5.2

- Design tool з JSON schema
- Implement few-shot prompting з 2–4 examples
- Validation-retry loops з specific error feedback
- Batch processing для 1000 documents
- Confidence scoring + human review routing
- Time budget: 18 hours

### Project 4: Agentic Customer Support System

**Domains:** 1.1–1.5, 2.1–2.3, 5.2–5.3

- Design 4+ MCP tools z clear descriptions
- Implement agentic loop з error handling
- Multi-step workflows z programmatic enforcement (hooks)
- Escalation patterns z explicit criteria
- Error propagation для coordinator recovery
- Time budget: 20 hours

### Project 5: Long-Session Codebase Analysis

**Domains:** 3.4, 5.1, 5.4–5.5

- Extended session: map large codebase structure
- Scratchpad persistence, context degradation detection
- Subagent delegation for verbose output isolation
- Session resumption + fork_session для alternatives
- Confidence scoring + human review integration
- Time budget: 15 hours

---

## Mock Exam Scenarios to Practice

### Scenario 1: Customer Support Resolution Agent

- Implement agent з escalation logic + tool enforcement
- Handle 3-issue customer requests z parallel processing
- Practice error handling, boundary validation
- Estimated: 8–10 hours study + hands-on

### Scenario 2: Code Generation with Claude Code

- Configure CLAUDE.md, path-specific rules, custom commands
- Plan mode для large refactoring, direct execution для small fixes
- Practice iterative refinement z input/output examples
- Estimated: 6–8 hours

### Scenario 3: Multi-Agent Research System

- Coordinator + subagents (search, analysis, synthesis, verification)
- Parallel execution, context passing, error propagation
- Coverage tracking, conflicting source handling
- Estimated: 10–12 hours

### Scenario 4: Developer Productivity Tools

- MCP tool design + built-in tool selection (Read, Write, Grep, Bash)
- Codebase exploration patterns
- Estimated: 6–8 hours

### Scenario 5: Claude Code in CI/CD

- Non-interactive mode (-p flag), structured output (JSON schema)
- Prior findings context, false positive management
- Estimated: 6–8 hours

### Scenario 6: Structured Data Extraction

- JSON schemas, validation-retry, few-shot examples
- Batch processing, confidence calibration, human review routing
- Estimated: 8–10 hours

---

## Self-Assessment Checklist

**Before Exam, Verify You Can:**

**Domain 1: Agentic Architecture**

- [ ] Implement agentic loop (stop_reason handling, tool results in history)
- [ ] Design multi-agent coordinator-subagent system з parallel execution
- [ ] Write hooks for policy enforcement + data normalization
- [ ] Create task decomposition strategies (fixed vs adaptive)
- [ ] Use session resumption + fork_session effectively
- [ ] Troubleshoot tool selection misrouting за descriptions

**Domain 2: Tool Design & MCP**

- [ ] Write clear tool descriptions differentiating similar tools
- [ ] Implement structured error responses з categories + retryable flags
- [ ] Configure tool distribution across agents з appropriate scoping
- [ ] Set up project + user MCP servers з env var expansion
- [ ] Select appropriate built-in tools (Grep, Glob, Read, Write, Edit, Bash)

**Domain 3: Claude Code**

- [ ] Configure CLAUDE.md hierarchy (user/project/directory)
- [ ] Create path-specific rules (.claude/rules/) з glob patterns
- [ ] Create project commands + personal skills з frontmatter
- [ ] Apply plan mode vs direct execution correctly
- [ ] Integrate into CI/CD pipelines (-p flag, --json-schema)
- [ ] Use iterative refinement techniques (I/O examples, test-driven)

**Domain 4: Prompting & Structured Output**

- [ ] Write explicit review criteria (not vague instructions)
- [ ] Create few-shot examples для ambiguous scenarios
- [ ] Design JSON schemas z optional/nullable fields
- [ ] Implement validation-retry loops з specific error feedback
- [ ] Calculate batch processing frequency for SLA constraints
- [ ] Design multi-pass + multi-instance review architectures

**Domain 5: Context Management & Reliability**

- [ ] Extract + persist transactional facts from verbose contexts
- [ ] Implement escalation criteria з explicit examples
- [ ] Propagate structured errors для coordinator recovery decisions
- [ ] Use scratchpad files + subagent delegation за long sessions
- [ ] Calibrate confidence scores на validation sets
- [ ] Preserve source attribution through multi-stage synthesis

---

## Final Tips

1. **Hands-on is critical.** Theoretical understanding isn't sufficient. Build every system from exercises.
2. **Practice the scenarios** from the exam guide multiple times until patterns are intuitive.
3. **Read error messages carefully.** Many questions test whether you understand why something failed.
4. **Context matters.** Pay attention to which domain each practice question tests; don't memorize answers.
5. **60% is passing.** You don't need 100%. Focus on the most common, highest-leverage patterns first.

---

## Additional Resources

- **Anthropic Official Docs:** https://docs.claude.com/
- **Exam Guide (provided):** Full scenarios, sample questions, domain weightings
- **Claude API Reference:** tool_use, stop_reason, max_tokens configuration
- **Agent SDK Documentation:** Hook patterns, AgentDefinition, Task tool
- **MCP Specification:** Tool descriptions, error handling, resources
- **Claude Code Guide:** CLAUDE.md hierarchy, rules, commands, skills
- **Practice Exam:** Complete before exam (separate link from Anthropic)

---
