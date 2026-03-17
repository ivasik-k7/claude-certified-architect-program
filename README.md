# Claude Certified Architect – Comprehensive Study Program

A structured, hands-on learning program for preparing and passing the **Claude Certified Architect – Foundations** certification exam.

## 🎯 Overview

This repository contains a complete preparation guide for the Claude Certified Architect – Foundations certification, including:

- **5 Core Domains** with detailed explanations
- **35+ Topics** covering all exam content
- **100+ Hours** of recommended study material
- **Hands-on Exercises** with working code examples
- **Real-world Scenarios** from the official exam
- **Practice Questions** and self-assessment checklists

**Target:** Achieve a passing score (720+) on the first attempt.

---

## 📚 Planned | Repository Structure

```
claude-certified-architect-program/
├── README.md                                          # This file
├── ROADMAP.md                                         # Full 120-150 hour study plan
├──
├── domains/
│   ├── 1-agentic-architecture-orchestration/
│   │   ├── 1-1-agentic-loop-lifecycle.md
│   │   ├── 1-2-multi-agent-orchestration.md
│   │   ├── 1-3-subagent-invocation.md
│   │   ├── 1-4-multi-step-workflows.md
│   │   ├── 1-5-agent-sdk-hooks.md
│   │   ├── 1-6-task-decomposition.md
│   │   ├── 1-7-session-state.md
│   │   └── exercises/
│   │       ├── exercise-1-basic-agentic-loop.py
│   │       ├── exercise-2-customer-support-agent.py
│   │       └── solutions/
│   │
│   ├── 2-tool-design-mcp-integration/
│   │   ├── 2-1-tool-interfaces.md
│   │   ├── 2-2-error-responses.md
│   │   ├── 2-3-tool-distribution.md
│   │   ├── 2-4-mcp-integration.md
│   │   ├── 2-5-built-in-tools.md
│   │   └── exercises/
│   │
│   ├── 3-claude-code-workflows/
│   │   ├── 3-1-claude-md-hierarchy.md
│   │   ├── 3-2-commands-skills.md
│   │   ├── 3-3-path-specific-rules.md
│   │   ├── 3-4-plan-mode.md
│   │   ├── 3-5-iterative-refinement.md
│   │   ├── 3-6-ci-cd-integration.md
│   │   └── exercises/
│   │
│   ├── 4-prompt-engineering-structured-output/
│   │   ├── 4-1-explicit-criteria.md
│   │   ├── 4-2-few-shot-prompting.md
│   │   ├── 4-3-json-schemas.md
│   │   ├── 4-4-validation-retry.md
│   │   ├── 4-5-batch-processing.md
│   │   ├── 4-6-multi-pass-reviews.md
│   │   └── exercises/
│   │
│   └── 5-context-management-reliability/
│       ├── 5-1-long-context.md
│       ├── 5-2-escalation-patterns.md
│       ├── 5-3-error-propagation.md
│       ├── 5-4-codebase-exploration.md
│       ├── 5-5-confidence-calibration.md
│       ├── 5-6-information-provenance.md
│       └── exercises/
│
├── projects/
│   ├── project-1-multi-agent-research-system/
│   │   ├── README.md
│   │   ├── main.py
│   │   ├── requirements.txt
│   │   └── example_output.md
│   │
│   ├── project-2-claude-code-team-workflows/
│   │   ├── README.md
│   │   ├── .claude-config/
│   │   │   ├── CLAUDE.md
│   │   │   ├── rules/
│   │   │   ├── commands/
│   │   │   └── skills/
│   │   └── example-project/
│   │
│   ├── project-3-structured-extraction-pipeline/
│   │   ├── README.md
│   │   ├── main.py
│   │   └── validation_schemas/
│   │
│   ├── project-4-customer-support-agent/
│   │   ├── README.md
│   │   ├── agent.py
│   │   └── tools/
│   │
│   └── project-5-long-session-codebase-analysis/
│       ├── README.md
│       └── implementation.py
│
├── exam-scenarios/
│   ├── scenario-1-customer-support.md
│   ├── scenario-2-code-generation.md
│   ├── scenario-3-multi-agent-research.md
│   ├── scenario-4-developer-productivity.md
│   ├── scenario-5-ci-cd-integration.md
│   └── scenario-6-data-extraction.md
│
├── resources/
│   ├── official-exam-guide.pdf                       # From Anthropic
│   ├── api-reference.md
│   ├── quick-start-guides/
│   │   ├── google-colab-setup.md
│   │   ├── local-setup.md
│   │   └── replit-setup.md
│   ├── glossary.md
│   └── useful-links.md
│
├── notes/
│   ├── week-1-notes.md
│   ├── week-2-notes.md
│   └── ...
│
└── progress-tracking/
    ├── PROGRESS.md                                    # Your personal tracking
    ├── self-assessment-checklist.md
    └── completed-exercises.md
```

---

## 🎓 Domains Overview

### **Domain 1: Agentic Architecture & Orchestration** (27% of exam)

Build intelligent agents that think, plan, and act autonomously.

- ✅ Agentic loop lifecycle
- ✅ Multi-agent orchestration (hub-and-spoke)
- ✅ Subagent invocation & context passing
- ✅ Multi-step workflows with enforcement
- ✅ Agent SDK hooks for deterministic control
- ✅ Task decomposition strategies
- ✅ Session management & resumption

**Time:** 100+ hours | **Difficulty:** ⭐⭐⭐⭐

---

### **Domain 2: Tool Design & MCP Integration** (18% of exam)

Design reliable tools and integrate external systems via Model Context Protocol.

- ✅ Effective tool interfaces & descriptions
- ✅ Structured error responses
- ✅ Tool distribution across agents
- ✅ MCP server integration & configuration
- ✅ Built-in tools (Grep, Glob, Read, Write, Bash)

**Time:** 73 hours | **Difficulty:** ⭐⭐⭐

---

### **Domain 3: Claude Code Configuration & Workflows** (20% of exam)

Configure Claude Code for team development and CI/CD pipelines.

- ✅ CLAUDE.md hierarchy (user/project/directory)
- ✅ Custom commands & skills with frontmatter
- ✅ Path-specific rules with glob patterns
- ✅ Plan mode vs direct execution
- ✅ Iterative refinement techniques
- ✅ CI/CD pipeline integration

**Time:** 86 hours | **Difficulty:** ⭐⭐⭐

---

### **Domain 4: Prompt Engineering & Structured Output** (20% of exam)

Master prompt design for precision and reliability.

- ✅ Explicit criteria to reduce false positives
- ✅ Few-shot prompting for consistency
- ✅ Structured output via JSON schemas
- ✅ Validation & retry loops
- ✅ Batch processing strategies
- ✅ Multi-pass & multi-instance reviews

**Time:** 84 hours | **Difficulty:** ⭐⭐⭐⭐

---

### **Domain 5: Context Management & Reliability** (15% of exam)

Handle long contexts, escalations, and multi-source synthesis reliably.

- ✅ Long-context management strategies
- ✅ Escalation & ambiguity resolution
- ✅ Error propagation in multi-agent systems
- ✅ Long codebase exploration patterns
- ✅ Confidence calibration & human review
- ✅ Information provenance & source attribution

**Time:** 84 hours | **Difficulty:** ⭐⭐⭐⭐

---

## 🗓️ Recommended Study Schedule

### **Week 1–2: Foundations (20 hours)**

- Domain 1.1–1.2 (Agentic Loop + Multi-Agent Basics)
- Domain 2.1–2.2 (Tool Design + Error Handling)
- Read full exam guide; take notes on scenarios

### **Week 3–4: Core Agent Patterns (25 hours)**

- Domain 1.3–1.7 (Subagent Invocation, Workflows, Hooks, Decomposition, Sessions)
- Build 2–3 complete agentic systems

### **Week 5: Tools & MCP (20 hours)**

- Domain 2.3–2.5 (Tool Distribution, MCP, Built-in Tools)
- Set up team MCP configuration (hands-on)

### **Week 6–7: Claude Code & Configuration (20 hours)**

- Domain 3.1–3.6 (CLAUDE.md, Commands, Skills, Plan Mode, Workflows)
- Configure real project with hierarchy + rules + skills

### **Week 8: Prompting & Structured Output (20 hours)**

- Domain 4.1–4.6 (Criteria, Few-Shot, JSON Schemas, Validation, Batch, Multi-Pass)
- Iterative refinement exercises

### **Week 9: Context & Reliability (20 hours)**

- Domain 5.1–5.6 (Context Management, Escalation, Error Propagation, Provenance)
- Build end-to-end systems with all patterns

### **Week 10: Integration & Practice (15 hours)**

- Combine all domains: build 1 large multi-agent system
- Complete practice exam
- Review weak areas

**Total: 120–150 hours over 10 weeks**

---

## 💻 Getting Started

### 1. Clone Repository

```bash
git clone https://github.com/ivasik-k7/claude-certified-architect-program.git
cd claude-certified-architect-program
```

### 2. Set Up Python Environment

```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

pip install anthropic python-dotenv
```

### 3. Configure API Key

Create a `.env` file in the root directory:

```
ANTHROPIC_API_KEY=your-api-key-here
```

Get your API key from: https://console.anthropic.com/

### 4. Start with Domain 1.1

```bash
# Read the guide
cat domains/1-agentic-architecture-orchestration/1-1-agentic-loop-lifecycle.md

# Run the basic example
cd domains/1-agentic-architecture-orchestration/exercises
python exercise-1-basic-agentic-loop.py
```

### 5. Track Your Progress

Update `progress-tracking/PROGRESS.md` as you complete topics.

---

## 🚀 Quick Links

### **Official Resources**

- [Claude API Documentation](https://docs.claude.com/)
- [Agent SDK Guide](https://docs.claude.com/en/docs/agents)
- [MCP Specification](https://modelcontextprotocol.io/)
- [Exam Guide PDF](./resources/official-exam-guide.pdf)

### **Setup Guides**

- [Google Colab Setup](./resources/quick-start-guides/google-colab-setup.md) ← Recommended for quick start
- [Local Setup (Anaconda)](./resources/quick-start-guides/local-setup.md)
- [Replit Setup](./resources/quick-start-guides/replit-setup.md)

### **Study Materials**

- [Full Roadmap](./ROADMAP.md) – 120–150 hour plan
- [Self-Assessment Checklist](./progress-tracking/self-assessment-checklist.md)
- [Glossary of Terms](./resources/glossary.md)
- [Useful Links & References](./resources/useful-links.md)

---

## 📊 Progress Tracking

Track your progress using:

- **PROGRESS.md** – Mark topics as In Progress / Complete / Reviewed
- **self-assessment-checklist.md** – Skills checklist before exam
- **completed-exercises.md** – Log your exercise completions

Example:

```markdown
## Week 1 Progress

- [x] Domain 1.1: Agentic Loop Lifecycle (Complete)
  - [x] Read guide
  - [x] Exercise 1: Trace Through Loop
  - [x] Exercise 2: Code Basic Loop
- [ ] Domain 1.2: Multi-Agent Orchestration (In Progress)
  - [x] Read guide
  - [ ] Exercise 1: Design Coordinator
  - [ ] Exercise 2: Implement System
```

---

## 🎯 Exam Information

**Format:** Multiple Choice (4 options per question)

**Scoring:** 100–1,000 points (720+ = Pass)

**Scenarios:** 4 random scenarios from 6 possible:

1. Customer Support Resolution Agent
2. Code Generation with Claude Code
3. Multi-Agent Research System
4. Developer Productivity Tools
5. Claude Code in CI/CD
6. Structured Data Extraction

**Coverage:**

- Domain 1: 27%
- Domain 2: 18%
- Domain 3: 20%
- Domain 4: 20%
- Domain 5: 15%

---

## 💡 Study Tips

### ✅ DO:

- **Hands-on first**: Code before theory
- **Complete exercises**: Don't skip practice
- **Build projects**: Integrate multiple domains
- **Take notes**: Highlight key patterns
- **Test frequently**: Use Colab, Replit, or local setup
- **Review scenarios**: Practice real exam situations

### ❌ DON'T:

- Memorize code examples (understand patterns)
- Skip anti-patterns (learn what NOT to do)
- Rush through foundations (Domain 1 is critical)
- Ignore error handling (reliability matters)
- Copy-paste without understanding (modify & test)

---

## 🤝 Contributing

This is a personal study repository, but contributions are welcome:

1. Found a typo or error? Open an issue.
2. Have a better explanation? Submit a pull request.
3. Built a cool project? Share it in `projects/`.
4. Have study tips? Add them to `resources/`.

---

## 📝 Notes

### Why This Structure?

- **Domains/** – Organized by exam domains for easy navigation
- **Projects/** – Real-world implementations combining multiple topics
- **Exam-scenarios/** – Practice with actual exam question types
- **Resources/** – Quick-reference guides and external links
- **Progress-tracking/** – Personal accountability and planning

### API Key Security

- ⚠️ **Never** commit `.env` files or hardcoded API keys
- ✅ Use environment variables: `os.getenv('ANTHROPIC_API_KEY')`
- ✅ Use `.gitignore` to exclude sensitive files

### Time Investment

- **Minimum:** 100 hours (tight schedule)
- **Recommended:** 120–150 hours (comfortable pace)
- **Ideal:** 150+ hours (deep mastery)

---

## 📞 Support & Questions

For questions about:

- **Exam content:** Refer to official exam guide in `resources/`
- **Code errors:** Check exercise solutions in `solutions/`
- **Concept confusion:** Re-read the domain guides, then ask
- **Project ideas:** See examples in `projects/`

---

## ✨ Key Takeaways

> **The agentic loop is your heartbeat.** Every iteration, Claude sees what happened last, decides what to do next, and your code makes it happen. Master this, and the rest becomes intuitive.

> **Context is everything.** How you structure message history, tool outputs, and cross-agent communication determines agent reliability. Pay attention to details.

> **Test relentlessly.** Theoretical understanding isn't sufficient. Build every system from exercises, test on real data, measure results.

---

## 📄 License

This study material is for educational purposes. The Claude Certified Architect certification is owned by Anthropic.

---

## 🎓 Good Luck!

You're taking on a substantial learning journey. This material is designed to get you to a **720+ passing score on the first attempt**.

**Stay consistent. Build projects. Test everything. You've got this.** 💪

---

**Last Updated:** March 2025
**Status:** 🟢 Active (Actively maintained during study period)
**Next Review:** Upon exam completion
