# Claude Certified Architect – Foundations

## Project Intelligence File

> **This file is the single source of truth for Claude when working in this repository.**
> Every session begins here. Read this file fully before taking any action.

---

## 🎯 Project Purpose

This repository is a **structured self-study program** for the **Claude Certified Architect – Foundations** certification exam. It is authored and maintained by **Ivan Kovtun**, a Solutions Architect / Cloud Software Engineer.

The program covers **5 certification domains**:

| #   | Domain                                | Focus Area                             |
| --- | ------------------------------------- | -------------------------------------- |
| 1   | Agentic Architecture & Orchestration  | Agents, tool use, multi-agent patterns |
| 2   | Prompt Engineering & Context Design   | Prompting strategies, context windows  |
| 3   | Safety, Alignment & Constitutional AI | RLHF, harmlessness, responsible AI     |
| 4   | Model Evaluation & Quality Assurance  | Evals, benchmarks, output quality      |
| 5   | Enterprise Deployment & Integration   | APIs, scalability, production patterns |

---

## 🧠 Content Standards

### Articles (`domains/*.md`)

- **Audience:** The author (Ivan), studying for certification
- **Tone:** Technical, precise, first-principles. No fluff.
- **Structure:** Always include:
  1. `## Overview` — 2–3 sentence summary
  2. `## Core Concepts` — key ideas with depth
  3. `## How It Works` — mechanics, architecture
  4. `## Exam Relevance` — what to remember for the cert
  5. `## Further Reading` — 2–3 links max
- **Length:** 600–1800 words per article
- **Diagrams:** Every article that has a system/flow component MUST link to a corresponding Mermaid diagram in `../diagrams/`

### Diagrams (`resources/*.mmd`)

- **Format:** Mermaid (`.mmd` files)
- **Naming:** `<concept-slug>.mmd` (e.g., `tool-use-flow.mmd`)
- **Types preferred:** `flowchart TD`, `sequenceDiagram`, `graph LR`, `C4Context`
- **Style rules:**
  - Always include a `%%{ title }%%` comment at the top
  - Use descriptive node labels (no abbreviations in node text)
  - Group related nodes with `subgraph`
  - Max complexity: 20 nodes before splitting into sub-diagrams

## ✍️ Writing & Editing Rules

1. **Never rewrite content without being asked.** Suggest, then act.
2. **Preserve the author's voice** — direct, technical, no filler phrases.
3. **Forbidden phrases:** "it's worth noting", "dive into", "leverage", "best-in-class", "robust solution", "comprehensive"
4. **Markdown hygiene:**
   - Max heading depth: `###`
   - Tables for comparisons (never unordered lists for comparisons)
   - Code blocks must specify language tag
5. **Citations:** When referencing Anthropic docs, link to `https://docs.anthropic.com` with the exact path

---

## 🔧 Workflow Protocols

### When asked to create a new article:

1. Check `domains/0X-*/README.md` for existing coverage to avoid duplication
2. Follow the Article structure above
3. Create a corresponding diagram if the topic is visual
4. Add entry to `progress/tracker.md`

### When asked to review/improve content:

1. Read the full file first
2. Identify: accuracy gaps, missing exam context, structural issues
3. Present a **diff-style summary** of proposed changes
4. Apply only after confirmation

### When generating diagrams:

1. Output valid Mermaid syntax
2. Validate mentally: no circular references that Mermaid can't render
3. Always wrap in a `.mmd` code block for copy-paste
4. Provide a 1-sentence alt-text description after the diagram

### When creating quizzes:

1. Pull from the article content in the same domain
2. Balance cognitive levels: 40% RECALL, 40% APPLY, 20% ANALYZE
3. Avoid trick questions — this is a study tool, not a gotcha

---

## 🚫 Guardrails

- **Do NOT** generate content outside the 5 domains without explicit instruction
- **Do NOT** add dependencies, scripts, or CI configs unless asked
- **Do NOT** modify `progress/` files without confirmation — these are Ivan's personal tracking
- **Do NOT** hallucinate Anthropic API details — check against the official docs pattern or flag uncertainty
- **Always ask** before creating new directories

---

## 📌 Current Focus

> Update this section manually as domains are completed.

- **Active Domain:** Domain 1 — Agentic Architecture & Orchestration
- **Status:** In progress
- **Next milestone:** Complete all articles + 1 quiz for Domain 1

---

## 🔗 Key External References

- Anthropic Docs: https://docs.anthropic.com
- Anthropic Cookbook: https://github.com/anthropics/anthropic-cookbook
- Model Card: https://www.anthropic.com/claude
- Prompt Library: https://docs.anthropic.com/en/prompt-library/library
- Claude API Reference: https://docs.anthropic.com/en/api/getting-started
