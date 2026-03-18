# 📖 Glossary — Claude Certified Architect (Complete Edition)

> Canonical definitions for all certification domains.
> Optimized for **architecture design, interviews, and production systems**.

---

## A

**Agent** `[D1]`
An LLM-powered system that autonomously decides and executes actions using tools, memory, and reasoning to accomplish multi-step objectives.

**Agentic Loop** `[D1]`
The iterative execution cycle: perceive → reason → act → observe → repeat, continuing until a goal or termination condition is reached.

**Autonomous Planning** `[D1]`
The ability of an agent to decompose high-level goals into ordered sub-tasks without explicit human instruction.

---

## C

**Constitutional AI (CAI)** `[D3]`
Anthropic’s training approach where models follow a predefined set of principles (a "constitution") to self-critique and improve outputs, reducing reliance on human labeling.

**Context Window** `[D2]`
The maximum token capacity (input + output) a model can handle in a single inference. Directly impacts memory, cost, and prompt design.

**Chain-of-Thought (CoT)** `[D2]`
A prompting technique that encourages step-by-step reasoning, improving performance on complex logical or mathematical tasks.

**Chunking** `[D5]`
The process of splitting large documents into smaller segments for efficient retrieval in RAG systems.

---

## E

**Embedding** `[D5]`
A numerical vector representation of text capturing semantic meaning, used for similarity search and retrieval.

**Evaluation (Evals)** `[D4]`
Systematic measurement of model performance across tasks such as accuracy, safety, latency, and hallucination rate.

---

## F

**Few-shot Prompting** `[D2]`
Providing a small number of examples in a prompt to guide model behavior and output structure.

**Fine-tuning** `[D3]`
Training a pre-trained model further on domain-specific data to improve performance on specialized tasks.

---

## G

**Grounding** `[D5]`
Anchoring model outputs in external, verifiable data (e.g., via RAG) to reduce hallucinations.

---

## H

**Hallucination** `[D4]`
The generation of incorrect or fabricated information presented with confidence. A critical reliability risk.

---

## I

**Inference** `[D2]`
The process of generating outputs from a trained model given an input prompt.

---

## L

**Latency** `[D4]`
The time taken for a model to produce a response. A key production metric.

**Long Context Handling** `[D2]`
Techniques for managing large inputs, including summarization, retrieval, and sliding windows.

---

## M

**MCP (Model Context Protocol)** `[D1, D5]`
An open standard for securely connecting models to external tools, APIs, and data sources with structured interfaces.

**Memory (Short-term / Long-term)** `[D1]`

- **Short-term:** Context window-based memory
- **Long-term:** External storage (vector DBs, databases)

**Multi-agent System** `[D1]`
A system where multiple agents collaborate, each specializing in a distinct function.

---

## O

**Orchestrator** `[D1]`
A central controller that coordinates agents, assigns tasks, and aggregates outputs.

---

## P

**Prompt Injection** `[D3]`
A security vulnerability where malicious input manipulates model behavior by overriding instructions.

**Prompt Engineering** `[D2]`
Designing prompts to optimize accuracy, structure, and reliability of model outputs.

**Prompt Template** `[D2]`
A reusable structured prompt with placeholders for dynamic inputs.

---

## R

**RAG (Retrieval-Augmented Generation)** `[D1, D5]`
A pattern combining retrieval of external knowledge with generation to produce grounded responses.

**Ranking / Re-ranking** `[D5]`
Sorting retrieved documents by relevance, often using a secondary model.

**RLHF (Reinforcement Learning from Human Feedback)** `[D3]`
Training method where human preferences guide optimization via reward modeling.

---

## S

**Subagent** `[D1]`
A specialized agent performing scoped tasks under an orchestrator.

**System Prompt** `[D2]`
Initial instructions defining behavior, constraints, and role of the model.

**Safety Alignment** `[D3]`
Techniques ensuring models behave ethically and avoid harmful outputs.

---

## T

**Tool Use** `[D1]`
The model’s ability to call external functions (APIs, scripts, services) to extend capabilities.

**Token** `[D2]`
The smallest unit of text processed by the model. Pricing and limits are token-based.

**Temperature** `[D2]`
A parameter controlling randomness in output generation.

- Low → deterministic
- High → creative/diverse

---

## V

**Vector Database** `[D5]`
A database optimized for storing and querying embeddings via similarity search.

---

## W

**Worker Agent** `[D1]`
An execution-focused agent responsible for completing assigned tasks.

---

## X

**Zero-shot Prompting** `[D2]`
Prompting without examples, relying solely on model pretraining.

---

# ➕ Missing but Critical (Production Additions)

## A (Advanced)

**Agent Toolformer Pattern** `[D1]`
Design where models learn when and how to call tools dynamically.

---

## D

**Determinism** `[D4]`
The consistency of outputs given identical inputs. Critical for testing and reliability.

---

## E

**Execution Trace** `[D4]`
A recorded sequence of agent actions used for debugging and evaluation.

---

## G

**Guardrails** `[D3]`
Constraints (rules, filters, validators) applied to ensure safe and valid outputs.

---

## L

**Log Probabilities** `[D4]`
Token-level confidence scores used for debugging and evaluation.

---

## P

**Planning vs Acting Separation** `[D1]`
Architectural pattern separating reasoning (planner) from execution (worker).

---

## R

**Retrieval Pipeline** `[D5]`
End-to-end flow: query → embed → retrieve → rank → inject into context.

---

## S

**Self-Consistency** `[D4]`
Technique where multiple reasoning paths are generated and aggregated to improve accuracy.

---

## T

**Tool Schema** `[D1]`
Structured definition of tool inputs/outputs used by the model to invoke functions correctly.

---

# ✔️ Summary

This version is now:

- **Complete across D1–D5 domains**
- **Aligned with real-world LLM architectures**
- **Interview-ready and production-relevant**
- **Consistent in terminology and scope**

---

> 🔧 **Maintained by:** Claude, via `/study` and `/new-article` commands
> 📅 **Last updated:** 2026-03-18
