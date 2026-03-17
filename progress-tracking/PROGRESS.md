# Claude Certified Architect – Preparation Progress Tracker

**Student:** Ivan Kovtun  
**Start Date:** March 17, 2025  
**Target Exam Date:** TBD (Recommended: Late April / Early May 2025)  
**Goal:** 720+ Score (Pass on First Attempt)

---

## 📊 Overall Progress

```
Total Estimated Hours: 150
Hours Completed: 0
Hours Remaining: 150
Completion: 0%

Target Weekly: 15 hours
```

### Progress Bar
```
████████████████████ 0/150 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

## 🎯 Study Schedule Overview

| Week | Domain | Focus | Hours | Status |
|------|--------|-------|-------|--------|
| 1–2 | 1.1–1.2 | Foundations (Agentic Loop + Multi-Agent) | 20 | 🔴 Not Started |
| 3–4 | 1.3–1.7 | Core Patterns (Subagent, Workflows, Hooks) | 25 | 🔴 Not Started |
| 5 | 2.1–2.5 | Tools & MCP | 20 | 🔴 Not Started |
| 6–7 | 3.1–3.6 | Claude Code Configuration | 20 | 🔴 Not Started |
| 8 | 4.1–4.6 | Prompt Engineering | 20 | 🔴 Not Started |
| 9 | 5.1–5.6 | Context & Reliability | 20 | 🔴 Not Started |
| 10 | All | Integration & Practice Exam | 15 | 🔴 Not Started |
| **TOTAL** | | | **150** | |

**Legend:** 🔴 Not Started | 🟡 In Progress | 🟢 Complete | 🔵 Reviewed

---

## 📚 DOMAIN 1: Agentic Architecture & Orchestration (27%)

**Estimated Time:** 100 hours  
**Current Status:** 🔴 Not Started  
**Progress:** 0/100 hours (0%)

### Domain Progress Bar
```
████████████████████ 0/100 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

### 1.1: Agentic Loop Lifecycle (15 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/15

#### Learning Materials
- [ ] Read guide: `domains/1-agentic-architecture/1-1-agentic-loop-lifecycle.md`
- [ ] Review flow diagram (ASCII visualization)
- [ ] Study 🔴 Critical Points (4 key concepts)
- [ ] Review anti-patterns section (4 things NOT to do)
- [ ] Read Python code example (understand each line)

#### Practice & Exercises
- [ ] **Exercise 1:** Trace Through a Loop (manual, on paper)
  - [ ] Scenario: "What is the weather in Gdańsk?"
  - [ ] Trace: 6 steps of execution
  - [ ] Document: Step-by-step in notes/week-1-notes.md
  - Status: 🔴 Not Started | Date Completed: _______
  
- [ ] **Exercise 2:** Code Basic Agentic Loop (hands-on)
  - [ ] Set up environment (API key, Python, dependencies)
  - [ ] Platform chosen: ☐ Google Colab | ☐ Replit | ☐ Local
  - [ ] Copy code template from guide
  - [ ] Run with test input: "Add 10 and 5"
  - [ ] Modify to add 3rd tool (multiply)
  - [ ] Run with: "Add 10 and 5, multiply by 3"
  - [ ] Verify all iterations printed correctly
  - [ ] Save code to: `domains/1-agentic-architecture/exercises/exercise-2-basic-loop.py`
  - Status: 🔴 Not Started | Date Completed: _______

#### Self-Assessment Questions
Answer these to verify understanding:

- [ ] **Q1:** What does `stop_reason` mean? When is it `"tool_use"` vs `"end_turn"`?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q2:** Why do you need to append BOTH assistant response AND tool results to messages?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q3:** What happens if you don't preserve message history between iterations?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q4:** Why can't you use iteration count to determine loop termination?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q5:** Draw the loop flow: user request → Claude → tool → Claude → final answer
  - [ ] Diagram drawn in notes
  - Confidence: ☐ Low | ☐ Medium | ☐ High

#### Code Implementation Checkpoint
```python
# Verify your code has:
- [ ] Proper stop_reason checking (not text content or iteration count)
- [ ] Tool results appended to message history
- [ ] Loop continues on "tool_use", terminates on "end_turn"
- [ ] All iterations logged with details
- [ ] Works with 2+ different test inputs
```

#### Review & Validation
- [ ] Review guide one more time (focus on Critical Points)
- [ ] Re-read anti-patterns to understand what NOT to do
- [ ] Run code with 5+ different test inputs
- [ ] All self-assessment questions answered with high confidence
- [ ] Code saved and documented

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.2: Multi-Agent Orchestration – Hub-and-Spoke (20 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/20

#### Learning Materials
- [ ] Read: `domains/1-agentic-architecture/1-2-multi-agent-orchestration.md`
- [ ] Understand coordinator-subagent patterns
- [ ] Study: Risks of narrow decomposition
- [ ] Study: Iterative refinement loops
- [ ] Review: Parallel subagent execution

#### Conceptual Exercises
- [ ] **Exercise 1:** Design Multi-Agent System (on paper)
  - Scenario: Research "impact of AI on creative industries"
  - [ ] Design coordinator agent (responsibilities, prompts)
  - [ ] Design 3–4 subagent types (search, analysis, synthesis, verification)
  - [ ] Define communication protocol (how data flows)
  - [ ] Document task decomposition (how topics split across subagents)
  - [ ] Estimate: how many iterations before complete research?
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** Implement Multi-Agent System (code)
  - [ ] Create coordinator agent class
  - [ ] Create 3 subagent classes (different specializations)
  - [ ] Implement Task tool for coordinator to spawn subagents
  - [ ] Implement parallel execution (multiple Task calls in one response)
  - [ ] Pass context explicitly to each subagent
  - [ ] Implement iterative refinement (coordinator evaluates synthesis, re-delegates)
  - [ ] Test on 3 different research topics
  - [ ] Measure: parallel vs sequential execution time
  - [ ] Save code to: `domains/1-agentic-architecture/exercises/exercise-2-multi-agent.py`
  - Status: 🔴 Not Started | Date Completed: _______

#### Self-Assessment Questions

- [ ] **Q1:** Why doesn't subagent automatically inherit coordinator's context?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q2:** How does coordinator decide which subagents to invoke?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q3:** What are risks of overly narrow task decomposition?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q4:** How do you spawn multiple subagents in parallel?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

- [ ] **Q5:** How does iterative refinement work? When does coordinator re-delegate?
  - Answer: _______________________________________________
  - Confidence: ☐ Low | ☐ Medium | ☐ High

#### Code Implementation Checkpoint
```python
# Verify your code has:
- [ ] Coordinator agent with AgentDefinition
- [ ] 3+ subagent classes with different specializations
- [ ] Explicit context passing to subagents (not inheritance)
- [ ] Parallel subagent execution (multiple Task calls)
- [ ] Coordinator evaluation logic (detect synthesis gaps)
- [ ] Re-delegation mechanism (coordinator calls subagents again)
- [ ] Execution time measurements
```

#### Testing & Validation
- [ ] Run on 3+ research topics
- [ ] Verify parallel execution is faster than sequential
- [ ] Verify each subagent received all necessary context
- [ ] Verify coordinator correctly identified coverage gaps
- [ ] All self-assessment questions answered with high confidence

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.3: Subagent Invocation, Context Passing, Spawning (18 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/18

#### Learning Materials
- [ ] Read: Domain guide
- [ ] Study: Task tool mechanism
- [ ] Study: AgentDefinition structure
- [ ] Study: Explicit context passing patterns
- [ ] Study: Structured data formats (separating content from metadata)

#### Practical Exercises
- [ ] **Exercise 1:** Design AgentDefinitions
  - [ ] Create 3 AgentDefinition objects (search, analysis, synthesis)
  - [ ] Write clear descriptions for each
  - [ ] Define system prompts (detailed roles)
  - [ ] Specify allowedTools for each
  - [ ] Document expected input/output format
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** Implement Context Passing
  - [ ] Structure metadata separately (URLs, dates, document names)
  - [ ] Pass complete findings explicitly in subagent prompts
  - [ ] Verify subagent can reference all prior findings
  - [ ] Test with 2+ documents per subagent
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 3:** Parallel Subagent Execution
  - [ ] Emit multiple Task calls in single coordinator response
  - [ ] Measure execution time vs sequential
  - [ ] Verify all subagents started simultaneously
  - [ ] Collect all results before proceeding
  - Status: 🔴 Not Started | Date Completed: _______

#### Self-Assessment Questions

- [ ] **Q1:** What is Task tool and how does it spawn subagents?
- [ ] **Q2:** How do you structure metadata when passing context between agents?
- [ ] **Q3:** When should you use parallel subagent execution?
- [ ] **Q4:** What happens if subagent context is incomplete?
- [ ] **Q5:** How do you handle fork_session for divergent approaches?

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.4: Multi-Step Workflows – Enforcement & Handoff (16 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/16

#### Learning Materials
- [ ] Read: Guide on workflow enforcement
- [ ] Study: Programmatic enforcement vs prompt-based
- [ ] Study: Hook patterns for prerequisite gates
- [ ] Study: Structured handoff protocols

#### Exercises
- [ ] **Exercise 1:** Design Customer Support Workflow
  - [ ] Define workflow steps (verification → issue handling → resolution/escalation)
  - [ ] Identify critical steps requiring enforcement
  - [ ] Design handoff structure (what data to include when escalating)
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** Implement Enforcement
  - [ ] Create hook that blocks process_refund until get_customer completes
  - [ ] Implement multi-concern decomposition
  - [ ] Test with requests combining 3+ issues
  - [ ] Verify parallel processing
  - Status: 🔴 Not Started | Date Completed: _______

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.5: Agent SDK Hooks (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

#### Learning Materials
- [ ] Read: Guide on hooks
- [ ] Study: PostToolUse hook patterns
- [ ] Study: Tool call interception
- [ ] Study: Data normalization examples

#### Exercises
- [ ] **Exercise 1:** Implement Data Normalization Hook
  - [ ] Create PostToolUse hook
  - [ ] Transform: Unix timestamp → ISO 8601
  - [ ] Transform: numeric codes → labels
  - [ ] Test with various tool results
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** Implement Policy Enforcement Hook
  - [ ] Create tool call interception hook
  - [ ] Block refunds > $500
  - [ ] Redirect to escalation workflow
  - [ ] Test with various refund amounts
  - Status: 🔴 Not Started | Date Completed: _______

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.6: Task Decomposition Strategies (16 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/16

#### Exercises
- [ ] **Exercise 1:** Fixed Pipeline for Code Review
  - [ ] Implement Stage 1: per-file local analysis
  - [ ] Implement Stage 2: cross-file integration
  - [ ] Test on 3 multi-file PRs
  - [ ] Measure: attention quality per file, cross-file consistency
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** Adaptive Decomposition
  - [ ] Map codebase structure
  - [ ] Identify high-impact areas
  - [ ] Generate adaptive plan
  - [ ] Adjust tasks based on findings
  - [ ] Test on legacy codebases
  - Status: 🔴 Not Started | Date Completed: _______

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 1.7: Session State, Resumption, Forking (12 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/12

#### Exercises
- [ ] **Exercise 1:** Session Resumption
  - [ ] Start investigation session (save with name)
  - [ ] Close session
  - [ ] Resume with `--resume <session-name>`
  - [ ] Verify prior context used
  - [ ] Inform agent of file changes
  - [ ] Continue investigation
  - Status: 🔴 Not Started | Date Completed: _______

- [ ] **Exercise 2:** fork_session for Alternatives
  - [ ] Analyze codebase (baseline)
  - [ ] fork_session: Approach A
  - [ ] fork_session: Approach B
  - [ ] Compare results
  - [ ] Choose best approach
  - Status: 🔴 Not Started | Date Completed: _______

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

## 📊 DOMAIN 2: Tool Design & MCP Integration (18%)

**Estimated Time:** 73 hours  
**Current Status:** 🔴 Not Started  
**Progress:** 0/73 hours (0%)

### Domain Progress Bar
```
████████████████████ 0/73 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

### 2.1: Effective Tool Interfaces & Descriptions (16 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/16

- [ ] Read guide
- [ ] Exercise 1: Write clear tool descriptions (differentiate 5 similar tools)
- [ ] Exercise 2: Tool selection reliability testing
- [ ] All self-assessment questions answered
- [ ] Tools successfully differentiated in testing

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 2.2: Structured Error Responses (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Read guide
- [ ] Exercise 1: Implement error response structure
- [ ] Exercise 2: Error handling in agent
- [ ] Test 20+ error scenarios
- [ ] Agent recovery verified for each error type

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 2.3: Tool Distribution & tool_choice (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Read guide
- [ ] Exercise 1: Design scoped tool access
- [ ] Exercise 2: Implement tool_choice configs
- [ ] Test selection reliability for each agent
- [ ] Verify no cross-role tool misuse

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 2.4: MCP Server Integration (15 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/15

- [ ] Read guide
- [ ] Set up project .mcp.json
- [ ] Configure 2 MCP servers
- [ ] Set up user ~/.claude.json
- [ ] Both servers available in session
- [ ] Test tool selection vs built-in tools

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 2.5: Built-in Tools (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Read guide
- [ ] Exercise 1: Grep usage for content search
- [ ] Exercise 2: Glob for file patterns
- [ ] Exercise 3: Read/Write/Edit operations
- [ ] Exercise 4: Bash commands
- [ ] Trace function usage through codebase
- [ ] Measure tool selection efficiency

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

## 🎨 DOMAIN 3: Claude Code Configuration & Workflows (20%)

**Estimated Time:** 86 hours  
**Current Status:** 🔴 Not Started  
**Progress:** 0/86 hours (0%)

### Domain Progress Bar
```
████████████████████ 0/86 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

### 3.1: CLAUDE.md Hierarchy & Configuration (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Read guide
- [ ] Create user-level ~/.claude/CLAUDE.md
- [ ] Create project ./CLAUDE.md
- [ ] Create directory-level rules
- [ ] Use @import for modular rules
- [ ] Test: new team member gets correct rules
- [ ] Verify using /memory command

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 3.2: Custom Commands & Skills (16 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/16

- [ ] Create project-scoped commands (.claude/commands/)
- [ ] Create user-scoped commands (~/.claude/commands/)
- [ ] Implement skills with SKILL.md
- [ ] Configure context: fork for isolation
- [ ] Configure allowed-tools restrictions
- [ ] Test invocation and isolation

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 3.3: Path-Specific Rules (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Create 5 .claude/rules/ files
- [ ] Use YAML frontmatter with glob patterns
- [ ] Test: rules load only for matching files
- [ ] Measure token usage: scoped vs monolithic
- [ ] Verify all test files use same conventions

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 3.4: Plan Mode vs Direct Execution (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Test direct execution on simple tasks
- [ ] Test plan mode on complex tasks
- [ ] Hybrid workflow: plan + direct execute
- [ ] Measure: time, iterations, rework
- [ ] Document when each mode is appropriate

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 3.5: Iterative Refinement (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Input/output examples for clarity
- [ ] Test-driven iteration
- [ ] Interview pattern before implementation
- [ ] Sequential vs parallel issue resolution
- [ ] Measure improvement curves

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 3.6: CI/CD Pipeline Integration (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Set up Claude Code in CI pipeline
- [ ] Use -p flag for non-interactive
- [ ] Configure --json-schema for output
- [ ] Add CLAUDE.md with standards
- [ ] Test on 5+ real PRs
- [ ] Iterate on false positives
- [ ] Document setup and tuning

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

## 🧠 DOMAIN 4: Prompt Engineering & Structured Output (20%)

**Estimated Time:** 84 hours  
**Current Status:** 🔴 Not Started  
**Progress:** 0/84 hours (0%)

### Domain Progress Bar
```
████████████████████ 0/84 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

### 4.1: Explicit Criteria for Precision (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Define 5–6 explicit review criteria
- [ ] Create code review prompt (vague vs explicit)
- [ ] Test on 10 PRs, measure false positives
- [ ] Disable high false positive category
- [ ] Iterate with explicit criteria
- [ ] Document improvement

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 4.2: Few-Shot Prompting (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Create few-shot examples for ambiguous scenarios
- [ ] v1: instructions only
- [ ] v2: 2 examples
- [ ] v3: 4 examples
- [ ] Test all versions, measure accuracy improvement
- [ ] Document effectiveness curve

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 4.3: Structured Output via JSON Schemas (16 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/16

- [ ] Design extraction tool with JSON schema
- [ ] Implement required/optional fields
- [ ] Include enum with "other" option
- [ ] Use nullable fields
- [ ] Test on 20 documents
- [ ] Verify no hallucination on missing fields
- [ ] Compare syntax error rates: schema vs text

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 4.4: Validation & Retry Loops (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Implement validation rules
- [ ] Create retry flow with error feedback
- [ ] Test: format errors (retry-able)
- [ ] Test: missing info (not retry-able)
- [ ] Add detected_pattern field
- [ ] Analyze dismissal patterns
- [ ] Measure retry success rate

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 4.5: Batch Processing (12 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/12

- [ ] Design batch requests (1000 documents)
- [ ] Implement custom_id correlation
- [ ] Handle batch failures and resubmit
- [ ] Calculate SLA frequency
- [ ] Compare costs: sync vs batch
- [ ] Measure processing time and savings

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 4.6: Multi-Pass & Multi-Instance Review (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Design multi-pass review (per-file + integration)
- [ ] Test on 45-file PR
- [ ] Independent review instance comparison
- [ ] Confidence calibration
- [ ] Route low-confidence to human review
- [ ] Measure effectiveness improvement

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

## 🔗 DOMAIN 5: Context Management & Reliability (15%)

**Estimated Time:** 84 hours  
**Current Status:** 🔴 Not Started  
**Progress:** 0/84 hours (0%)

### Domain Progress Bar
```
████████████████████ 0/84 hours
□□□□□□□□□□□□□□□□□□□□ 0%
```

---

### 5.1: Long-Context Management (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Design persistent "case facts" block
- [ ] Implement context optimization techniques
- [ ] Test on multi-issue sessions
- [ ] Measure: context window usage, accuracy
- [ ] Verify fact retention at context boundaries

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 5.2: Escalation & Ambiguity Resolution (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Define escalation criteria with examples
- [ ] Implement in system prompt
- [ ] Test on 30 support scenarios
- [ ] Measure escalation precision
- [ ] Iterate on false positives
- [ ] Document final criteria

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 5.3: Error Propagation (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Design structured error responses
- [ ] Implement coordinator recovery logic
- [ ] Simulate: transient failures
- [ ] Simulate: valid empty results
- [ ] Simulate: access failures
- [ ] Measure recovery success rate

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 5.4: Long Codebase Exploration (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Implement scratchpad file persistence
- [ ] Use subagent delegation for exploration
- [ ] Test session resumption with crash recovery
- [ ] Measure context degradation
- [ ] Compare approaches: direct vs delegation vs scratchpad

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 5.5: Confidence Calibration (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Implement field-level confidence scores
- [ ] Create validation set (500 documents)
- [ ] Calibrate confidence thresholds
- [ ] Analyze accuracy by: field, document type
- [ ] Route low-confidence to human review
- [ ] Measure automation gains vs review workload

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

### 5.6: Information Provenance & Synthesis (14 hours)

**Status:** 🔴 Not Started  
**Hours Logged:** 0/14

- [ ] Design claim-source mapping structure
- [ ] Preserve attribution through synthesis
- [ ] Handle conflicting data with source annotation
- [ ] Include temporal data (publication dates)
- [ ] Render appropriately (tables, prose, lists)
- [ ] Test with 3+ source synthesis

**Completion Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete → 🔵 Reviewed  
**Estimated Completion Date:** _________________

---

## 🎯 Capstone Projects (Combined Learning)

**Status:** 🔴 Not Started

### Project 1: Multi-Agent Research System
**Domains:** 1.1–1.7, 2.1–2.5, 5.3, 5.6  
**Estimated Time:** 20 hours  
**Status:** 🔴 Not Started

- [ ] Build coordinator + 3–4 subagents
- [ ] Implement MCP tools with structured errors
- [ ] Multi-pass workflow with feedback
- [ ] Handle conflicting sources + provenance
- [ ] All tests passing

**Completion Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Project 2: Claude Code Workflows for Team
**Domains:** 3.1–3.6, 2.4  
**Estimated Time:** 15 hours  
**Status:** 🔴 Not Started

- [ ] Configure CLAUDE.md hierarchy
- [ ] Create .claude/rules/ with patterns
- [ ] Create commands + skills
- [ ] Integrate MCP servers
- [ ] Set up plan + direct execution workflows

**Completion Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Project 3: Structured Data Extraction Pipeline
**Domains:** 4.1–4.5, 5.1, 5.2  
**Estimated Time:** 18 hours  
**Status:** 🔴 Not Started

- [ ] Design tool with JSON schema
- [ ] Few-shot prompting (2–4 examples)
- [ ] Validation-retry loops
- [ ] Batch processing (1000 documents)
- [ ] Confidence scoring + human review

**Completion Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Project 4: Agentic Customer Support System
**Domains:** 1.1–1.5, 2.1–2.3, 5.2–5.3  
**Estimated Time:** 20 hours  
**Status:** 🔴 Not Started

- [ ] Design 4+ MCP tools
- [ ] Implement agentic loop
- [ ] Multi-step workflows with hooks
- [ ] Escalation patterns
- [ ] Error propagation

**Completion Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Project 5: Long-Session Codebase Analysis
**Domains:** 3.4, 5.1, 5.4–5.5  
**Estimated Time:** 15 hours  
**Status:** 🔴 Not Started

- [ ] Extended session exploration
- [ ] Scratchpad persistence
- [ ] Subagent delegation
- [ ] Session resumption + fork
- [ ] Confidence scoring

**Completion Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

## 📋 Exam Scenario Practice

**Status:** 🔴 Not Started

### Scenario 1: Customer Support Resolution Agent
- [ ] Read scenario description
- [ ] Design solution (2 hours)
- [ ] Implement system (4 hours)
- [ ] Test with 20+ cases
- [ ] Refine based on results
- [ ] Time to complete: _____

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Scenario 2: Code Generation with Claude Code
- [ ] Read scenario description
- [ ] Design CLAUDE.md structure
- [ ] Implement commands + skills
- [ ] Test plan mode vs direct execution
- [ ] Refine based on results

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Scenario 3: Multi-Agent Research System
- [ ] Read scenario description
- [ ] Design agent architecture
- [ ] Implement all components
- [ ] Test on 5+ research topics
- [ ] Measure quality + coverage

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Scenario 4: Developer Productivity Tools
- [ ] Read scenario description
- [ ] Design tool suite
- [ ] Implement with built-in tools
- [ ] Test codebase exploration
- [ ] Measure effectiveness

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Scenario 5: Claude Code in CI/CD
- [ ] Read scenario description
- [ ] Set up pipeline
- [ ] Implement code review + test generation
- [ ] Test on real PRs
- [ ] Measure false positive rates

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

### Scenario 6: Structured Data Extraction
- [ ] Read scenario description
- [ ] Design extraction tool
- [ ] Implement validation-retry
- [ ] Test on varied documents
- [ ] Measure accuracy + precision

**Practice Date:** _________________ | **Status:** 🔴 Not Started → 🟡 In Progress → 🟢 Complete

---

## 🧪 Pre-Exam Verification Checklist

**Overall Readiness:** 0% | 🔴 Not Ready

### Domain 1 Readiness
- [ ] Can implement agentic loop from scratch (no reference)
- [ ] Can design multi-agent coordinator system
- [ ] Understand all anti-patterns
- [ ] Can explain stop_reason behavior
- [ ] Confident on: Hooks, decomposition, sessions

**Readiness Score:** ___ / 10 | **Status:** 🔴

---

### Domain 2 Readiness
- [ ] Can write clear tool descriptions
- [ ] Can implement structured error responses
- [ ] Can configure tool_choice correctly
- [ ] Can set up MCP servers
- [ ] Can use built-in tools effectively

**Readiness Score:** ___ / 10 | **Status:** 🔴

---

### Domain 3 Readiness
- [ ] Can configure CLAUDE.md hierarchy
- [ ] Can create path-specific rules
- [ ] Can implement commands + skills
- [ ] Can decide plan mode vs direct execution
- [ ] Can integrate into CI/CD

**Readiness Score:** ___ / 10 | **Status:** 🔴

---

### Domain 4 Readiness
- [ ] Can write explicit review criteria
- [ ] Can design few-shot examples
- [ ] Can create JSON schemas
- [ ] Can implement validation-retry
- [ ] Can design batch processing

**Readiness Score:** ___ / 10 | **Status:** 🔴

---

### Domain 5 Readiness
- [ ] Can manage long contexts
- [ ] Can design escalation patterns
- [ ] Can implement error propagation
- [ ] Can handle long codebase exploration
- [ ] Can calibrate confidence scores

**Readiness Score:** ___ / 10 | **Status:** 🔴

---

## 📊 Weekly Study Summary

### Week 1
**Planned:** 15 hours | **Actual:** 0 hours | **Status:** 🔴

Topics Covered:
- [ ] Domain 1.1: Agentic Loop Lifecycle
- [ ] Domain 1.2: Multi-Agent Orchestration

Notes:
```
(Add your weekly notes here)
```

Challenges Encountered:
```
(Describe any difficulties)
```

Key Learnings:
```
(Summarize what you learned)
```

---

### Week 2
**Planned:** 15 hours | **Actual:** 0 hours | **Status:** 🔴

Topics Covered:
- [ ] Domain 1.3: Subagent Invocation
- [ ] Domain 1.4: Workflows & Enforcement

Notes:
```
(Add notes)
```

---

### Week 3
**Planned:** 15 hours | **Actual:** 0 hours | **Status:** 🔴

(Continue pattern...)

---

## 🎯 Final Exam Preparation (Week 10)

**Status:** 🔴 Not Started

### Practice Exam
- [ ] Complete full practice exam (2–3 hours)
- [ ] Score: ___ / 1000
- [ ] Pass/Fail: _______________
- [ ] Date Completed: ___________

### Weak Areas to Review
```
(Document any domains/topics with <80% confidence)
```

### Final Review Plan
```
(Outline final 1–2 days before exam)
```

### Exam Confidence Rating
**Before Final Review:** ___ / 10  
**After Final Review:** ___ / 10

---

## 📅 Important Dates

| Event | Planned Date | Actual Date | Status |
|-------|------------|------------|--------|
| Start Study | March 17, 2025 | | 🔴 |
| Domain 1 Complete | April 1, 2025 | | 🔴 |
| Domain 2 Complete | April 15, 2025 | | 🔴 |
| Domain 3 Complete | April 29, 2025 | | 🔴 |
| Domain 4 Complete | May 13, 2025 | | 🔴 |
| Domain 5 Complete | May 27, 2025 | | 🔴 |
| All Projects Complete | June 10, 2025 | | 🔴 |
| Practice Exam | June 16, 2025 | | 🔴 |
| Real Exam | Late June 2025 | | 🔴 |

---

## 💬 Notes & Reflections

### General Study Notes
```
(Add general observations, study tips, etc.)
```

### Code Implementation Notes
```
(Record challenges, solutions, patterns you discovered)
```

### Exam Content Reflections
```
(Your thoughts on the material, difficulty, relevance)
```

### Personal Insights
```
(Any "aha!" moments, breakthrough realizations)
```

---

## 📈 Statistics & Metrics

### Hours Invested
- **Week 1–2:** 0 / 20 hours
- **Week 3–4:** 0 / 25 hours
- **Week 5:** 0 / 20 hours
- **Week 6–7:** 0 / 20 hours
- **Week 8:** 0 / 20 hours
- **Week 9:** 0 / 20 hours
- **Week 10:** 0 / 15 hours
- **Total:** 0 / 150 hours

### Exercise Completion Rate
- **Exercises Assigned:** 35+
- **Exercises Completed:** 0
- **Completion Rate:** 0%

### Project Status
- **Projects Planned:** 5
- **Projects Complete:** 0
- **Completion Rate:** 0%

### Domain Coverage
- **Total Topics:** 35+
- **Topics Complete:** 0
- **Coverage:** 0%

---

## ✅ Sign-Off

**Last Updated:** March 17, 2025  
**Next Review:** [Date]  
**Overall Progress:** 0%

**Comments:**
```
Ready to start! 🚀
```

---

**Remember:** Consistency over intensity. Study a bit every day. You've got this! 💪
