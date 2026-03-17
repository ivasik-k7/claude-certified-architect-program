# Agentic Loop — Детальне Пояснення

## Три КРИТИЧНІ лінії коду

### 1️⃣ **Додавання assistant response до messages**

```javascript
// ITERATION 1 (після першого API call):
messages.push({
  role: "assistant",
  content: response.content, // Це може містити { type: "tool_use" }
});
```

**ЧОМ ЦЕ КРИТИЧНО:**

- Модель повинна знати, ЩО ВОНА САМА СКАЗАЛА (що запросила інструмент)
- Без цього в наступному API call модель не буде мати контексту своїх рішень
- Це попереджує циклічні помилки (модель не будуть повторювати одне й те ж)

**Що в `response.content`:**

```javascript
[
  {
    type: "text",
    text: "Let me search for information about...",
  },
  {
    type: "tool_use",
    id: "call_abc123",
    name: "web_search",
    input: { query: "Claude latest news" },
  },
];
```

---

### 2️⃣ **Додавання tool result як user message**

```javascript
// ПІСЛЯ виконання інструменту:
messages.push({
  role: "user",
  content: [
    {
      type: "tool_result",
      tool_use_id: "call_abc123", // ПОВИНЕН ЗБІГАТИСЬ з ID в assistant message!
      content: "Tim Cook is CEO of Apple...",
    },
  ],
});
```

**ЧОМ ЦЕ КРИТИЧНО:**

- tool_result приходить з ролі "user" (тобто від "зовнішнього світу")
- `tool_use_id` ПОВИНЕН збігатися з ID інструменту, який модель запросила
- Без цього модель не розуміє, ЧИЇМ результатом вона отримує дані
- Це дозволяє моделі скоординувати отримані результати з її запитом

**Структура на цьому етапі:**

```javascript
messages = [
  { role: "user", content: "What's AI news?" },
  { role: "assistant", content: [{ type: "tool_use", id: "call_abc123", ... }] },
  { role: "user", content: [{ type: "tool_result", tool_use_id: "call_abc123", ... }] }
  // Модель ТЕПЕР вводить цей контекст в наступний API call
]
```

---

### 3️⃣ **Перевірка stop_reason (не iteration count!)**

```javascript
if (response.stop_reason === "end_turn") {
  // ГОТОВО — модель сказала що вона завершила
  return response.content;
} else if (response.stop_reason === "tool_use") {
  // ПРОДОВЖИТИ — модель запросила інструмент
  // Виконати інструмент, додати результати, повторити loop
} else if (response.stop_reason === "max_tokens") {
  // ПОМИЛКА — контекст переповнений
}
```

**ЧОМ `iteration < MAX_ITERATIONS` недостатньо:**

| Сценарій                         | Потребує кроків | Проблема з iteration count                           |
| -------------------------------- | --------------- | ---------------------------------------------------- |
| Прості запитання ("What's 2+2?") | 1               | Цикл продовжується непотрібно після готово           |
| Web search                       | 2-3             | Цикл може закінчитися не маючи повної інформації     |
| Research з fetch                 | 5-7             | Цикл може закінчитися преждевременно                 |
| Глибокий аналіз                  | 10+             | Цикл закінчується, хоча модель потреує більше кроків |

**Правильна логіка:**

```javascript
while (true) {
  const response = await api.call({ messages });

  // ПЕРШОЧЕРГОВО перевір stop_reason
  if (response.stop_reason === "end_turn") break;
  if (response.stop_reason === "max_tokens") {
    // Handle gracefully
    break;
  }

  // ПОТІМ перевір iteration (як failsafe)
  if (iteration >= MAX_ITERATIONS) {
    console.warn("Max iterations reached before stop_reason");
    break;
  }

  // Обробити tool_use...
  iteration++;
}
```

---

## Message History Evolution — Комплетний Приклад

```javascript
// СТАН 1: Користувач запитує
messages = [
  { role: "user", content: "What's latest on Claude AI?" }
];
// API call → stop_reason: "tool_use"

// СТАН 2: Додаємо assistant response (з tool_use)
messages = [
  { role: "user", content: "What's latest on Claude AI?" },
  { role: "assistant", content: [
    { type: "text", text: "Let me search..." },
    { type: "tool_use", id: "call_1", name: "web_search", input: { query: "..." } }
  ] }
];

// СТАН 3: Виконуємо інструмент, додаємо результат
messages = [
  { role: "user", content: "What's latest on Claude AI?" },
  { role: "assistant", content: [
    { type: "text", text: "Let me search..." },
    { type: "tool_use", id: "call_1", name: "web_search", input: { query: "..." } }
  ] },
  { role: "user", content: [
    { type: "tool_result", tool_use_id: "call_1", content: "Found 3 results:\n1. Claude 3.5...\n2. Claude vs ChatGPT..." }
  ] }
];
// API call → stop_reason: "tool_use" (需要 fetch)

// СТАН 4: Додаємо другий assistant response
messages = [
  { role: "user", content: "..." },
  { role: "assistant", content: [...] },
  { role: "user", content: [...] },
  { role: "assistant", content: [
    { type: "text", text: "Let me fetch more details..." },
    { type: "tool_use", id: "call_2", name: "fetch", input: { url: "..." } }
  ] }
];

// СТАН 5: Додаємо результат другого інструменту
messages = [
  { role: "user", content: "..." },
  { role: "assistant", content: [...] },
  { role: "user", content: [...] },
  { role: "assistant", content: [...] },
  { role: "user", content: [
    { type: "tool_result", tool_use_id: "call_2", content: "Full article about Claude 3.5 Sonnet..." }
  ] }
];
// API call → stop_reason: "end_turn" (готово!)

// СТАН 6: Повертаємо фінальну відповідь
// messages історія була передана API
// stop_reason === "end_turn" означає модель готова дати відповідь
// Витягуємо { type: "text" } блок з response.content
```

---

## Типові Помилки й Як їх Уникнути

### ❌ Помилка 1: Додавання тільки tool result

```javascript
// НЕПРАВИЛЬНО:
messages.push({
  role: "user",
  content: toolResult, // Модель не знає що вона запросила!
});
```

**Симптом**: Модель запитує той ж інструмент багато разів або робить випадкові кроки.

**Рішення**:

```javascript
// ПРАВИЛЬНО:
messages.push({ role: "assistant", content: response.content });  // Спочатку
messages.push({ role: "user", content: [{ type: "tool_result", ... }] });  // Потім
```

---

### ❌ Помилка 2: Нехтування tool_use_id

```javascript
// НЕПРАВИЛЬНО:
messages.push({
  role: "user",
  content: [
    {
      type: "tool_result",
      content: "Result...",
      // Забув tool_use_id!
    },
  ],
});
```

**Симптом**: API помилка або модель не зв'язує результат з запитом.

**Рішення**:

```javascript
// ПРАВИЛЬНО:
const toolUseBlock = response.content.find((c) => c.type === "tool_use");
messages.push({
  role: "user",
  content: [
    {
      type: "tool_result",
      tool_use_id: toolUseBlock.id, // ✅ МУСИТЬ бути присутнім
      content: "Result...",
    },
  ],
});
```

---

### ❌ Помилка 3: Перезаповнення messages на кожній ітерації

```javascript
// НЕПРАВИЛЬНО:
for (let i = 0; i < 10; i++) {
  messages = [{ role: "user", content: "..." }]; // НОВИЙ масив кожен раз!
  const resp = await api.call({ messages });
  // Модель не пам'ятає попередніх кроків
}
```

**Симптом**: Модель робить однакові запити, цикл не збігається.

**Рішення**:

```javascript
// ПРАВИЛЬНО:
let messages = [{ role: "user", content: "..." }];  // Один масив
for (let i = 0; i < 10; i++) {
  const resp = await api.call({ messages });  // Переиспользуй той же масив
  messages.push({ role: "assistant", content: resp.content });
  messages.push({ role: "user", content: [{ type: "tool_result", ... }] });
}
```

---

### ❌ Помилка 4: Ігнорування stop_reason, спираючись на iteration

```javascript
// НЕПРАВИЛЬНО:
for (let i = 0; i < 5; i++) {
  const resp = await api.call({ messages });
  if (i >= 4) break; // Закінчи після 5 ітерацій, навіть якщо модель не готова
}
```

**Симптом**: Неповні відповіді, недостатня дослідження, знову й знову неправильні результати.

**Рішення**:

```javascript
// ПРАВИЛЬНО:
let iterations = 0;
while (iterations < MAX_ITERATIONS) {
  const resp = await api.call({ messages });

  if (resp.stop_reason === "end_turn") break; // 🎯 ПОТОЧНА УМОВА
  if (resp.stop_reason === "max_tokens") {
    // Додай контекст або збільш max_tokens
    break;
  }

  // Обробити tool_use...
  iterations++;
}
```

---

## Отримання Правильного API Key

```bash
# Встановіть SDK
npm install @anthropic-ai/sdk

# Встановіть API key
export ANTHROPIC_API_KEY="sk-ant-..."

# Запустіть скрипт
node agentic_loop.js
```

---
