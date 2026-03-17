# DOMAIN 1.1: Agentic Loop Lifecycle

## Детальний гайд з прикладами

---

## 🎯 КОНЦЕПЦІЯ: Що таке Agentic Loop?

**Agentic Loop** — це цикл взаємодії між твоєю програмою та Claude, де:

1. Твій код надсилає запит (prompt + tools definitions)
2. Claude аналізує й вирішує, потрібно викликати tool або просто відповісти
3. Claude повертає `stop_reason`:
   - `"tool_use"` = хочу викликати tool(s)
   - `"end_turn"` = готова дати фінальну відповідь
4. Якщо `stop_reason == "tool_use"`, твій код:
   - Виконує tool(s) що Claude запросив
   - Додає результат в conversation history
   - Надсилає запит back to Claude
5. Процес повторюється доки `stop_reason == "end_turn"`

**Ключова ідея:** Claude не просто дає відповідь. Вона **рішує** яка дія потрібна, а твій код **виконує** цю дію й повідомляє результат.

---

## 📊 FLOW DIAGRAM: Agentic Loop

```
┌─────────────────────────────────────────────────────────────┐
│ USER: "Додай 10 + 5, потім помнож результат на 3"         │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│ ITERATION 1: Claude API Call                                │
│ - messages: [...user prompt...]                             │
│ - tools: [{name: "add", params}, {name: "multiply", ...}]  │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
        ┌──────────────────────────────┐
        │ Claude Response:             │
        │ stop_reason: "tool_use"      │
        │ tool_use block:              │
        │ {                            │
        │   name: "add",               │
        │   input: {a: 10, b: 5}       │
        │ }                            │
        └──────────────────┬───────────┘
                           │
                           ▼
        ┌──────────────────────────────┐
        │ YOUR CODE: Execute Tool      │
        │ result = 10 + 5 = 15         │
        │                              │
        │ Append to messages:          │
        │ {                            │
        │   role: "user",              │
        │   content: [{                │
        │     type: "tool_result",     │
        │     tool_use_id: "...",      │
        │     content: "15"            │
        │   }]                         │
        │ }                            │
        └──────────────────┬───────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│ ITERATION 2: Claude API Call (з updated messages history)  │
│ - messages: [...previous history... + tool_result...]      │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
        ┌──────────────────────────────┐
        │ Claude Response:             │
        │ stop_reason: "tool_use"      │
        │ tool_use block:              │
        │ {                            │
        │   name: "multiply",          │
        │   input: {a: 15, b: 3}       │
        │ }                            │
        └──────────────────┬───────────┘
                           │
                           ▼
        ┌──────────────────────────────┐
        │ YOUR CODE: Execute Tool      │
        │ result = 15 * 3 = 45         │
        │                              │
        │ Append to messages           │
        └──────────────────┬───────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│ ITERATION 3: Claude API Call                                │
└─────────────────────┬───────────────────────────────────────┘
                      │
                      ▼
        ┌──────────────────────────────┐
        │ Claude Response:             │
        │ stop_reason: "end_turn"      │
        │ text: "10 + 5 = 15,          │
        │       15 * 3 = 45"           │
        │                              │
        │ NO tool_use blocks           │
        └──────────────────┬───────────┘
                           │
                           ▼
        ┌──────────────────────────────┐
        │ Loop terminates!             │
        │ Final answer: 45             │
        └──────────────────────────────┘
```

---

## 💻 PYTHON EXAMPLE: Basic Agentic Loop

```python
import anthropic
import json

# Initialize Claude client
client = anthropic.Anthropic(api_key="your-api-key")

# Define tools
TOOLS = [
    {
        "name": "add",
        "description": "Add two numbers together",
        "input_schema": {
            "type": "object",
            "properties": {
                "a": {"type": "number", "description": "First number"},
                "b": {"type": "number", "description": "Second number"},
            },
            "required": ["a", "b"],
        },
    },
    {
        "name": "multiply",
        "description": "Multiply two numbers",
        "input_schema": {
            "type": "object",
            "properties": {
                "a": {"type": "number", "description": "First number"},
                "b": {"type": "number", "description": "Second number"},
            },
            "required": ["a", "b"],
        },
    },
]

# Simple tool execution function
def execute_tool(tool_name: str, tool_input: dict) -> str:
    """Execute a tool and return the result"""
    if tool_name == "add":
        result = tool_input["a"] + tool_input["b"]
        print(f"🧮 Executing: add({tool_input['a']}, {tool_input['b']}) = {result}")
        return str(result)
    elif tool_name == "multiply":
        result = tool_input["a"] * tool_input["b"]
        print(f"🧮 Executing: multiply({tool_input['a']}, {tool_input['b']}) = {result}")
        return str(result)
    else:
        return f"Unknown tool: {tool_name}"

# Main agentic loop
def agentic_loop(user_message: str):
    """Run agentic loop until Claude says end_turn"""

    print(f"\n{'='*60}")
    print(f"USER REQUEST: {user_message}")
    print(f"{'='*60}\n")

    # Initialize messages with user request
    messages = [
        {"role": "user", "content": user_message}
    ]

    iteration = 0

    # MAIN LOOP: Continue while Claude uses tools
    while True:
        iteration += 1
        print(f"\n>>> ITERATION {iteration}")
        print(f"Sending {len(messages)} messages to Claude...")

        # Call Claude API
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            tools=TOOLS,
            messages=messages,
        )

        print(f"Claude response:")
        print(f"  - stop_reason: {response.stop_reason}")
        print(f"  - content blocks: {len(response.content)}")

        # Check stop_reason
        if response.stop_reason == "end_turn":
            # LOOP TERMINATES: Claude is done
            print(f"\n✅ Loop terminated (stop_reason='end_turn')")

            # Extract final answer from response
            final_text = ""
            for block in response.content:
                if hasattr(block, "text"):
                    final_text += block.text

            print(f"\n📝 FINAL ANSWER:\n{final_text}")
            return final_text

        elif response.stop_reason == "tool_use":
            # Claude wants to use tools
            print(f"\n🔧 Claude wants to use tools")

            # Process all tool_use blocks in Claude's response
            tool_results = []

            for block in response.content:
                if block.type == "tool_use":
                    tool_name = block.name
                    tool_input = block.input
                    tool_use_id = block.id

                    print(f"  - Tool: {tool_name}")
                    print(f"    Input: {json.dumps(tool_input, indent=6)}")

                    # Execute the tool
                    tool_result = execute_tool(tool_name, tool_input)

                    print(f"    Result: {tool_result}")

                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": tool_use_id,
                        "content": tool_result,
                    })

            # 🔴 CRITICAL STEP: Add Claude's response to messages history
            messages.append({
                "role": "assistant",
                "content": response.content
            })

            # 🔴 CRITICAL STEP: Add tool results to messages history
            messages.append({
                "role": "user",
                "content": tool_results
            })

            print(f"\n✅ Added assistant response + tool results to message history")
            print(f"   Total messages now: {len(messages)}")

        else:
            # Unexpected stop_reason
            print(f"⚠️  Unexpected stop_reason: {response.stop_reason}")
            break

    return "Loop ended unexpectedly"


# Run example
if __name__ == "__main__":
    # Test 1: Simple calculation
    agentic_loop("Add 10 and 5, then multiply the result by 3")

    # Test 2: More complex
    agentic_loop("First, add 100 and 50. Then multiply by 2. Finally, add 10 to that result.")
```

---

## 🔴 CRITICAL POINTS: What You MUST Understand

### 1️⃣ **stop_reason Determines Loop Control**

```python
# ❌ WRONG: Check if response has text
while response.content:  # This ALWAYS has content!
    # Will loop even on tool_use
    pass

# ✅ CORRECT: Check stop_reason explicitly
while response.stop_reason == "tool_use":
    # Only continues if Claude wants to use tools
    pass
```

### 2️⃣ **Tool Results MUST Be Added to Message History**

```python
# ❌ WRONG: Just execute tool, don't tell Claude
result = execute_tool("add", {"a": 10, "b": 5})
# Next Claude call doesn't know what you did!

# ✅ CORRECT: Add BOTH assistant response + tool result
messages.append({"role": "assistant", "content": response.content})
messages.append({"role": "user", "content": tool_results})
# Now Claude knows what happened
```

### 3️⃣ **Conversation History Accumulates**

```python
# Each iteration, messages grow:
# Iteration 1: [user_message]
# Iteration 2: [user_message, assistant_response, tool_result]
# Iteration 3: [user_message, assistant_response, tool_result, assistant_response, tool_result]
# ...

# ✅ This is GOOD — Claude retains context from all prior iterations
# This is what enables intelligent decision-making
```

### 4️⃣ **You Cannot Use Heuristics to Determine Loop End**

```python
# ❌ WRONG: Check for assistant text
if response.content[0].text:
    break  # WRONG! Tool_use responses might also have text

# ❌ WRONG: Count iterations
iteration_count = 0
while iteration_count < 5:
    iteration_count += 1
    # What if Claude needs 6 iterations? Premature stop!

# ❌ WRONG: Check if tool_use blocks exist
if not any(block.type == "tool_use" for block in response.content):
    break  # WRONG! Might still be "tool_use" stop_reason

# ✅ CORRECT: Only check stop_reason
if response.stop_reason == "end_turn":
    break
```

---

## 📋 STEP-BY-STEP: Message Structure

### Initial State:

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Add 10 and 5, then multiply by 3"
    }
  ]
}
```

### After Iteration 1 (tool_use response):

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Add 10 and 5, then multiply by 3"
    },
    {
      "role": "assistant",
      "content": [
        {
          "type": "tool_use",
          "id": "toolu_01...",
          "name": "add",
          "input": { "a": 10, "b": 5 }
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "tool_result",
          "tool_use_id": "toolu_01...",
          "content": "15"
        }
      ]
    }
  ]
}
```

### After Iteration 2 (another tool_use):

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Add 10 and 5, then multiply by 3"
    },
    {
      "role": "assistant",
      "content": [
        {
          "type": "tool_use",
          "id": "toolu_01...",
          "name": "add",
          "input": { "a": 10, "b": 5 }
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "tool_result",
          "tool_use_id": "toolu_01...",
          "content": "15"
        }
      ]
    },
    {
      "role": "assistant",
      "content": [
        {
          "type": "tool_use",
          "id": "toolu_02...",
          "name": "multiply",
          "input": { "a": 15, "b": 3 }
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "tool_result",
          "tool_use_id": "toolu_02...",
          "content": "45"
        }
      ]
    }
  ]
}
```

### After Iteration 3 (end_turn):

```json
{
  "messages": [
    // ... all previous messages ...
    {
      "role": "assistant",
      "content": [
        {
          "type": "text",
          "text": "I've completed your calculation:\n1. 10 + 5 = 15\n2. 15 × 3 = 45\n\nThe final answer is 45."
        }
      ]
    }
  ]
}
```

---

## 🧪 PRACTICE EXERCISE 1: Trace Through a Loop

**Scenario:** User says "What is the weather in Gdańsk?"

You have 2 tools:

- `get_weather(city)` → returns JSON with temp, condition
- `get_coordinates(city)` → returns lat, lon

**Your task:** Without running code, trace through what SHOULD happen:

1. What's the first API call you make to Claude? What messages do you send?
2. Claude responds with stop_reason = ?
3. What tool(s) does Claude choose?
4. After you execute the tool, what do you add to messages?
5. What's the second API call?
6. When does the loop terminate?

**Answer:**

```
1. First call:
   - messages: [{"role": "user", "content": "What is the weather in Gdańsk?"}]
   - tools: [get_weather, get_coordinates]

2. Claude responds:
   - stop_reason: "tool_use"
   - Claude realizes it needs to get weather for Gdańsk
   - tool_use block: {name: "get_weather", input: {city: "Gdańsk"}}

3. Tool executed:
   - get_weather("Gdańsk") → {temp: 5, condition: "cloudy", humidity: 80}

4. You add to messages:
   - {"role": "assistant", "content": [tool_use block from Claude]}
   - {"role": "user", "content": [{"type": "tool_result", "tool_use_id": "...", "content": "..."}]}

5. Second call:
   - messages now has: user request + assistant tool_use + user tool_result
   - Claude sees the weather data
   - stop_reason: "end_turn"
   - Claude responds with: "The weather in Gdańsk is 5°C and cloudy..."

6. Loop terminates because stop_reason == "end_turn"
```

---

## 🚨 COMMON ANTI-PATTERNS: What NOT To Do

### Anti-Pattern 1: Parsing Natural Language for Loop Control

```python
# ❌ WRONG
if "I'm done" in response.content[0].text:
    break

# Why it's wrong:
# - Claude might say "I'm done searching" but still need tool_use
# - Fragile and unpredictable
# - stop_reason is the ONLY reliable signal
```

### Anti-Pattern 2: Arbitrary Iteration Caps

```python
# ❌ WRONG
max_iterations = 5
for i in range(max_iterations):
    # Even if Claude needs 6 iterations!
    pass

# Why it's wrong:
# - Some tasks naturally require many iterations
# - You're truncating Claude's thinking
# - Task-dependent, not a general solution
```

### Anti-Pattern 3: Checking for Assistant Text Content

```python
# ❌ WRONG
if response.content and hasattr(response.content[0], "text"):
    break

# Why it's wrong:
# - Tool_use responses might also have text blocks
# - Tool_use responses have stop_reason: "tool_use"
# - You'll break too early
```

### Anti-Pattern 4: Not Preserving Full Message History

```python
# ❌ WRONG
messages = [{"role": "user", "content": tool_results}]  # Lost history!
response = client.messages.create(messages=messages, ...)

# Why it's wrong:
# - Claude loses context from previous iterations
# - Can't reason about what happened earlier
# - Quality degrades rapidly

# ✅ CORRECT
messages.append({"role": "assistant", "content": response.content})
messages.append({"role": "user", "content": tool_results})
response = client.messages.create(messages=messages, ...)
```

---

## 💡 THEORY: Why This Design?

**Why not just give Claude all data upfront?**

Example: You're building a customer support agent.

```python
# ❌ Bad approach: Give all tools, all data upfront
customer_data = fetch_all_customers()  # 10,000 customers!
response = client.messages.create(
    messages=[{"role": "user", "content": "Resolve customer issue"}],
    context=str(customer_data),  # Huge context!
)

# Problems:
# - Massive context window usage (expensive!)
# - Claude can't reason about what to fetch
# - Can't guide data retrieval iteratively

# ✅ Better approach: Agentic loop
# Give tools, Claude decides what data to fetch

messages = [{"role": "user", "content": "Resolve customer issue"}]
tools = [
    {"name": "search_customer", "description": "Search for customer by ID/email"},
    {"name": "get_orders", "description": "Fetch customer's orders"},
    {"name": "get_support_history", "description": "Fetch support tickets"},
]

# Now Claude:
# 1. Thinks "I need customer ID first"
# 2. Uses search_customer tool
# 3. Sees results
# 4. Decides "Now I need orders"
# 5. Uses get_orders tool
# 6. Sees results
# 7. Decides "I have enough info, let me respond"

# Benefits:
# - Only fetches data Claude needs (efficient!)
# - Claude reasons about what to fetch next
# - Interactive, intelligent workflow
```

---

## 🎯 PRACTICE EXERCISE 2: Code It Yourself

**Task:** Implement a basic agentic loop with these tools:

- `calculate_age(birth_year)` → current_year - birth_year
- `get_current_year()` → 2024

**Test case:** User says "I was born in 1990, how old am I?"

**What should happen:**

1. Claude realizes it needs current year
2. Calls `get_current_year()`
3. Claude realizes it needs to calculate
4. Calls `calculate_age(1990)`
5. Claude responds with final answer

**Your code:**

```python
# Start here!

def agentic_loop(user_input):
    messages = [...]

    while True:
        response = client.messages.create(...)

        if response.stop_reason == "...":
            # Terminate
            pass
        elif response.stop_reason == "...":
            # Handle tools
            pass
```

**Hint:** You've got the template above. Fill in the blanks!

---

## ✅ SELF-CHECK: Do You Understand?

- [ ] What does `stop_reason` mean? When is it `"tool_use"` vs `"end_turn"`?
- [ ] Why do you need to append BOTH assistant response AND tool results to messages?
- [ ] What happens if you don't preserve message history?
- [ ] Why can't you use iteration count to determine loop end?
- [ ] Draw the loop flow (user → Claude → tool → Claude → final answer)
- [ ] Implement a basic agentic loop with 2 tools from scratch

---

## 🔗 NEXT STEPS

Once you've mastered this, move to **Theme 1.2: Multi-Agent Orchestration**.

This foundation is CRITICAL. Everything else in Domain 1 builds on this.

**Key Takeaway:**

> The agentic loop is your agent's heartbeat. Every iteration, Claude sees what happened last, decides what to do next, and your code makes it happen. That's it. Master this, and the rest becomes intuitive.
