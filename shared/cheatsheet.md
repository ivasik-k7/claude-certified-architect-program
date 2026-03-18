TODO

# ⚡ Exam Cheatsheet — Claude Certified Architect Foundations

> Quick-reference card. One entry per key concept.
> Generated and maintained via `/summarize --cheatsheet`.

---

## Domain 1 — Agentic Architecture & Orchestration

### Tool Use

| What         | Detail                                                                           |
| ------------ | -------------------------------------------------------------------------------- |
| Definition   | Model calls external functions defined in the request                            |
| Key pattern  | Define tools → model decides when to call → execute → return result to model     |
| Input format | JSON schema with `name`, `description`, `input_schema`                           |
| Exam tip     | Model decides IF and WHEN to use tools — you define availability, not invocation |

### Orchestrator / Subagent Pattern

| What        | Detail                                                                             |
| ----------- | ---------------------------------------------------------------------------------- |
| Definition  | Hierarchical multi-agent system with delegating orchestrator                       |
| When to use | Tasks too complex for single-agent context; parallelizable subtasks                |
| Exam trap   | Subagents are not inherently less capable — scope is constrained, not intelligence |

### MCP (Model Context Protocol)

| What          | Detail                                                                  |
| ------------- | ----------------------------------------------------------------------- |
| Definition    | Open standard for connecting models to tools/data                       |
| Key advantage | Standardized, reusable tool integrations across different models        |
| Exam tip      | MCP is protocol-level, not model-level — any compliant model can use it |

---

## Domain 2 — Prompt Engineering & Context Design

### System Prompt

| What       | Detail                                                                    |
| ---------- | ------------------------------------------------------------------------- |
| Definition | Pre-conversation instructions establishing behavior, persona, constraints |
| Position   | Always first; highest instruction priority                                |
| Exam tip   | Operators control system prompt; users control human turn only            |

### Chain-of-Thought (CoT)

| What           | Detail                                                         |
| -------------- | -------------------------------------------------------------- |
| Definition     | Eliciting step-by-step reasoning before final answer           |
| Trigger phrase | "Think step by step" / "Let's reason through this"             |
| When it helps  | Math, logic, multi-step reasoning; not for simple lookup tasks |

---

## Domain 3 — Safety, Alignment & Constitutional AI

### Constitutional AI

| What       | Detail                                                                    |
| ---------- | ------------------------------------------------------------------------- |
| Definition | Training via self-critique against a principled "constitution"            |
| Phases     | Supervised learning → RLAIF (AI feedback instead of human)                |
| Exam tip   | CAI reduces need for human labelers in the harmlessness fine-tuning phase |

### Prompt Injection

| What         | Detail                                                          |
| ------------ | --------------------------------------------------------------- |
| Definition   | Malicious content overriding model instructions via environment |
| Risk context | Agentic systems reading untrusted content (web, documents)      |
| Mitigation   | Sandboxing, explicit trust levels, input validation             |

---

## Domain 4 — Model Evaluation & Quality Assurance

_(Populate via `/summarize --cheatsheet domain 4`)_

---

## Domain 5 — Enterprise Deployment & Integration

_(Populate via `/summarize --cheatsheet domain 5`)_

---

> 📅 Last updated: 2026-03-18
