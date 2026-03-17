# DOMAIN 1.2: Multi-Agent Orchestration – Hub-and-Spoke

## Детальний гайд з прикладами коду

---

## 🎯 КОНЦЕПЦІЯ: Що таке Multi-Agent Orchestration?

**Multi-Agent Orchestration** — це паттерн, де **один координуючий агент** керує кількома **спеціалізованими субагентами**.

### Ключова ідея:

- ✅ **Coordinator** (дирижер) — розуміє завдання, розбиває на підтаски, керує субагентами
- ✅ **Subagents** (музиканти) — спеціалізовані на одному завданні, виконують його добре
- ✅ **Ізольований контекст** — кожен субагент НЕ знає про інших, тільки про свою задачу
- ✅ **Всесередня комунікація** — все проходить через coordinator (для observability)

### Порівняння:

```
❌ ANTI-PATTERN: Direct Communication (НЕПРАВИЛЬНО)
┌──────────────┐
│ Coordinator  │
└──────────────┘
       ▲ ▼
   ┌───┴────┐
   │ Web    │ ──────────────────┐
   │Search  │                   │
   └────────┘              ┌────▼──────┐
                           │ Synthesis  │ ← Search не повинен знати про synthesis
                           └────▲──────┘
   ┌──────────┐            │
   │ Analysis │────────────┘
   └──────────┘


✅ CORRECT PATTERN: Hub-and-Spoke (ПРАВИЛЬНО)
         ┌──────────────────┐
         │  Coordinator     │ ← Знає все
         │  (Hub)           │
         └──────────────────┘
                  │
      ┌───┬──────┼──────┬───┐
      │   │      │      │   │
      ▼   ▼      ▼      ▼   ▼
    ┌─┐ ┌─┐ ┌─┐ ┌─┐ ┌─┐
    │W│ │A│ │S│ │V│ │O│ ← Subagents
    │e│ │n│ │y│ │e│ │r│   (ізольовані)
    │b│ │a│ │n│ │r│ │c│
    └─┘ └─┘ └─┘ └─┘ └─┘
    (знають тільки про свою роботу)
```

---

## 📊 ARCHITECTURE DIAGRAM: Hub-and-Spoke

```
┌─────────────────────────────────────────────────────────────────┐
│                         USER                                     │
│              "Research impact of AI on creativity"              │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    COORDINATOR AGENT                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ Task: Understand request → Plan → Delegate → Aggregate  │  │
│  │                                                          │  │
│  │ Step 1: Parse request                                  │  │
│  │         "OK, це про AI і creativе industries"          │  │
│  │                                                          │  │
│  │ Step 2: Create decomposition plan:                     │  │
│  │         ├─ Visual Arts: paintings, digital art         │  │
│  │         ├─ Music: composition, performance             │  │
│  │         ├─ Writing: fiction, journalism                │  │
│  │         └─ Film: production, direction                 │  │
│  │                                                          │  │
│  │ Step 3: Spawn subagents for each area                 │  │
│  │         (передати контекст, очікувати результати)      │  │
│  │                                                          │  │
│  │ Step 4: Receive findings from all subagents           │  │
│  │         (синтезувати у unified report)                 │  │
│  └──────────────────────────────────────────────────────────┘  │
└──────────┬──────────┬──────────┬──────────┬──────────────────────┘
           │          │          │          │
   ┌───────▼────┐ ┌───▼──────┐ ┌─▼────────┐ ┌──▼──────────┐
   │  WEB        │ │ DOCUMENT │ │SYNTHESIS │ │VERIFICATION│
   │ SEARCH      │ │ ANALYSIS │ │ AGENT    │ │ AGENT      │
   │ SUBAGENT    │ │ SUBAGENT │ │          │ │            │
   ├─────────────┤ ├──────────┤ ├──────────┤ ├────────────┤
   │ Specialty:  │ │Specialty:│ │Specialty:│ │Specialty:  │
   │ Find web    │ │ Read &   │ │Combine   │ │ Check facts│
   │ articles    │ │ extract  │ │findings, │ │ against    │
   │ about AI    │ │ from     │ │identify  │ │ sources    │
   │ & visual    │ │academic  │ │ gaps     │ │            │
   │ arts        │ │papers    │ │          │ │            │
   │             │ │          │ │          │ │            │
   │ Context     │ │Context   │ │Context   │ │Context     │
   │ it gets:    │ │it gets:  │ │it gets:  │ │it gets:    │
   │ - Topic     │ │- Topic   │ │- Search  │ │- Synthesis │
   │ - Scope     │ │- Search  │ │- Docs    │ │ - Findings │
   │ - Keywords  │ │- URLs    │ │- Analysis│ │ - Claims   │
   │             │ │results   │ │findings  │ │ - Sources  │
   └─────────────┘ └──────────┘ └──────────┘ └────────────┘
        ▲ ▼           ▲ ▼          ▲ ▼           ▲ ▼
     (ізольовані контексти - NOT SHARED)
```

---

## 🔴 CRITICAL CONCEPT: Isolated Context

### ❌ WRONG: Subagents inherit context

```python
# НЕПРАВИЛЬНО!
coordinator_context = {
    "research_topic": "AI in creativity",
    "findings_from_web": [...],
    "findings_from_docs": [...],
}

# Передати як... що?
# Subagent НЕ знає що розуміти!
subagent_response = call_subagent(
    prompt="Do your thing",
    # context=coordinator_context  # Це НЕ працює!
)
```

**Проблема:** Subagent не отримає контекст!

### ✅ CORRECT: Explicit context in prompt

```python
# ПРАВИЛЬНО!
search_findings = [
    {"url": "article1.com", "content": "..."},
    {"url": "article2.com", "content": "..."},
]

doc_findings = [
    {"title": "Paper1", "key_points": ["...", "..."]},
    {"title": "Paper2", "key_points": ["...", "..."]},
]

synthesis_prompt = f"""
You are a research synthesis agent.

Your task: Combine findings from web search and document analysis
into a coherent report about AI's impact on creativity.

Web Search Findings:
{json.dumps(search_findings, indent=2)}

Document Analysis Findings:
{json.dumps(doc_findings, indent=2)}

Identify gaps:
- Is visual arts covered?
- Is music covered?
- Is writing covered?
- Is film covered?

Create a structured synthesis with:
1. Well-established findings (mentioned in multiple sources)
2. Contested findings (different sources disagree)
3. Coverage gaps (areas not researched)
"""

synthesis_response = call_subagent(prompt=synthesis_prompt)
```

**Key:** Весь контекст явно передається у prompt!

---

## 💻 PYTHON EXAMPLE: Multi-Agent Research System

```python
import anthropic
import json
from typing import Optional

client = anthropic.Anthropic(api_key="your-api-key")

# Define subagent specifications
SUBAGENT_DEFINITIONS = {
    "search_agent": {
        "name": "Search Agent",
        "description": "Searches web and returns relevant articles about a topic",
        "system_prompt": """You are a web research specialist.
Your job is to find relevant articles and information about topics.
Return findings as structured data with URLs and key points.""",
    },
    "analysis_agent": {
        "name": "Analysis Agent",
        "description": "Analyzes documents and extracts key insights",
        "system_prompt": """You are a document analyst.
Your job is to read and extract key insights from research papers and documents.
Return structured key findings with citations.""",
    },
    "synthesis_agent": {
        "name": "Synthesis Agent",
        "description": "Combines findings and creates comprehensive reports",
        "system_prompt": """You are a research synthesis specialist.
Your job is to combine findings from multiple sources into coherent narratives.
Identify well-established facts, contested claims, and coverage gaps.""",
    },
}

# Global tools for all subagents
COMMON_TOOLS = [
    {
        "name": "get_research_results",
        "description": "Mock tool: returns research results",
        "input_schema": {
            "type": "object",
            "properties": {
                "topic": {"type": "string", "description": "Topic to search"},
            },
            "required": ["topic"],
        },
    },
]


class MultiAgentOrchestrator:
    """Coordinator that manages subagents"""

    def __init__(self):
        self.research_findings = {
            "search": [],
            "analysis": [],
            "synthesis": None,
        }

    def coordinator_loop(self, user_request: str):
        """Main coordinator loop"""

        print(f"\n{'='*70}")
        print(f"USER REQUEST: {user_request}")
        print(f"{'='*70}\n")

        # Step 1: Coordinator analyzes request and creates plan
        print(">>> STEP 1: Coordinator Analyzing Request")
        plan = self._create_decomposition_plan(user_request)
        print(f"Plan created: {plan['subtasks']}\n")

        # Step 2: Spawn subagents (PARALLEL execution)
        print(">>> STEP 2: Spawning Subagents")
        print("⚡ Spawning PARALLEL tasks...")

        # Spawn search agent
        search_findings = self._spawn_search_agent(
            topic=plan["main_topic"],
            subtopics=plan["subtasks"][:2],  # Visual arts, Music
        )
        self.research_findings["search"] = search_findings

        # Spawn analysis agent
        analysis_findings = self._spawn_analysis_agent(
            topic=plan["main_topic"],
            search_results=search_findings,
        )
        self.research_findings["analysis"] = analysis_findings

        print(f"✅ Both subagents completed\n")

        # Step 3: Coordinator evaluates synthesis quality
        print(">>> STEP 3: Coordinator Evaluating Synthesis Quality")
        synthesis = self._spawn_synthesis_agent(
            topic=plan["main_topic"],
            search_results=search_findings,
            analysis_results=analysis_findings,
            subtasks=plan["subtasks"],
        )
        self.research_findings["synthesis"] = synthesis

        # Step 4: Check for coverage gaps
        print(">>> STEP 4: Checking Coverage Gaps")
        gaps = self._identify_gaps(synthesis)

        if gaps:
            print(f"⚠️  Found coverage gaps: {gaps}")
            print("Coordinator re-delegating to cover gaps...\n")

            # Re-delegate to search agent for missing topics
            additional_search = self._spawn_search_agent(
                topic=plan["main_topic"],
                subtopics=gaps,
            )
            search_findings.extend(additional_search)

            # Re-run synthesis with complete findings
            synthesis = self._spawn_synthesis_agent(
                topic=plan["main_topic"],
                search_results=search_findings,
                analysis_results=analysis_findings,
                subtasks=plan["subtasks"],
            )
            self.research_findings["synthesis"] = synthesis

        print("✅ Research complete!\n")
        return synthesis

    def _create_decomposition_plan(self, user_request: str) -> dict:
        """Coordinator creates a decomposition plan"""

        messages = [
            {
                "role": "user",
                "content": f"""Create a research decomposition plan for: {user_request}

Return JSON:
{{
    "main_topic": "...",
    "subtasks": ["area1", "area2", "area3", "area4"],
    "reasoning": "..."
}}"""
            }
        ]

        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            messages=messages,
        )

        # Parse response (simplified)
        text = response.content[0].text
        try:
            plan = json.loads(text[text.find('{'):text.rfind('}')+1])
        except:
            plan = {
                "main_topic": "AI and Creativity",
                "subtasks": ["Visual Arts", "Music", "Writing", "Film"],
                "reasoning": "Divided creativity into major artistic domains"
            }

        return plan

    def _spawn_search_agent(self, topic: str, subtopics: list) -> list:
        """Spawn search subagent"""

        print(f"  🔍 Search Agent starting (subtopics: {subtopics})")

        prompt = f"""You are a research specialist searching for information.

Main Topic: {topic}
Subtopics to research: {', '.join(subtopics)}

Find 2–3 key articles/sources for EACH subtopic.
Return as JSON array:
[
    {{
        "subtopic": "...",
        "source": "...",
        "url": "...",
        "key_points": ["point1", "point2", "point3"]
    }},
    ...
]

IMPORTANT: Provide realistic but hypothetical data."""

        messages = [{"role": "user", "content": prompt}]

        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=2000,
            messages=messages,
        )

        # Parse findings (simplified)
        text = response.content[0].text
        print(f"  ✅ Search Agent found results")

        # Return mock findings
        findings = [
            {
                "subtopic": subtopics[0] if subtopics else "Topic1",
                "source": "Article about impact of AI",
                "url": "https://example.com/article1",
                "key_points": [
                    "AI enables new creative possibilities",
                    "Concerns about job displacement",
                    "New tools for artists"
                ]
            },
            {
                "subtopic": subtopics[1] if len(subtopics) > 1 else "Topic2",
                "source": "Research paper on AI creativity",
                "url": "https://example.com/paper1",
                "key_points": [
                    "AI as collaborator vs replacement",
                    "Skills still required for quality work"
                ]
            }
        ]

        return findings

    def _spawn_analysis_agent(self, topic: str, search_results: list) -> list:
        """Spawn analysis subagent"""

        print(f"  📚 Analysis Agent starting")

        # This agent gets EXPLICIT context about search results
        prompt = f"""You are a research analyst. Analyze these web findings about: {topic}

Web Search Results:
{json.dumps(search_results, indent=2)}

Your task: Extract and analyze key insights.

For each result:
1. Identify main claims
2. Assess credibility
3. Find supporting evidence
4. Note any contradictions

Return as JSON:
[
    {{
        "source": "...",
        "main_claims": ["claim1", "claim2"],
        "credibility_score": 0.0-1.0,
        "evidence": "..."
    }},
    ...
]"""

        messages = [{"role": "user", "content": prompt}]

        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=2000,
            messages=messages,
        )

        print(f"  ✅ Analysis Agent completed analysis")

        # Mock analysis findings
        findings = [
            {
                "source": "Article 1",
                "main_claims": [
                    "AI enables new creative forms",
                    "Requires human supervision"
                ],
                "credibility_score": 0.85,
                "evidence": "Peer-reviewed sources cited"
            }
        ]

        return findings

    def _spawn_synthesis_agent(
        self,
        topic: str,
        search_results: list,
        analysis_results: list,
        subtasks: list,
    ) -> dict:
        """Spawn synthesis subagent"""

        print(f"  🔗 Synthesis Agent starting")

        # This agent gets COMPLETE context from both other agents
        prompt = f"""You are a research synthesis specialist.

Topic: {topic}

Web Research Findings:
{json.dumps(search_results, indent=2)}

Analysis Results:
{json.dumps(analysis_results, indent=2)}

Research Areas Expected: {', '.join(subtasks)}

Your task:
1. Synthesize findings into coherent narrative
2. Identify well-established facts (multiple sources agree)
3. Identify contested claims (sources disagree)
4. Identify coverage gaps (areas not well researched)

Return JSON:
{{
    "synthesis": "Narrative combining all findings...",
    "well_established": ["fact1", "fact2"],
    "contested": ["claim1", "claim2"],
    "coverage_gaps": ["missing_area1", "missing_area2"]
}}"""

        messages = [{"role": "user", "content": prompt}]

        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=3000,
            messages=messages,
        )

        print(f"  ✅ Synthesis Agent created report")

        # Mock synthesis
        synthesis = {
            "synthesis": "Comprehensive report on AI and creativity...",
            "well_established": [
                "AI is creating new creative tools",
                "Human creativity still essential"
            ],
            "contested": [
                "Impact on job market"
            ],
            "coverage_gaps": [
                "Long-term societal impact",
                "Ethical considerations"
            ]
        }

        return synthesis

    def _identify_gaps(self, synthesis: dict) -> list:
        """Coordinator identifies coverage gaps"""
        gaps = synthesis.get("coverage_gaps", [])
        print(f"   Coverage gaps identified: {gaps}")
        return gaps


# Example usage
def main():
    orchestrator = MultiAgentOrchestrator()

    result = orchestrator.coordinator_loop(
        "Research the impact of AI on creative industries including visual arts, music, writing, and film"
    )

    print("\n" + "="*70)
    print("FINAL SYNTHESIS REPORT")
    print("="*70)
    print(json.dumps(result, indent=2))


if __name__ == "__main__":
    main()
```

---

## 🔑 KEY CONCEPTS: Hub-and-Spoke Design

### 1. **Coordinator Responsibilities**

```python
class CoordinatorResponsibilities:
    """What coordinator MUST do"""

    def task_decomposition(self):
        """Break complex request into subtasks"""
        # "Research AI impact on creativity" →
        # [Visual Arts, Music, Writing, Film, Ethics]
        pass

    def dynamic_selection(self):
        """Choose which subagents to invoke based on findings"""
        # Not always use all subagents
        # If search agent finds ethics coverage, skip ethics subagent
        pass

    def context_passing(self):
        """Explicitly pass findings to other agents"""
        # Never assume subagents inherit context
        # Always include: prior findings, sources, metadata
        pass

    def result_aggregation(self):
        """Collect and combine subagent outputs"""
        # Wait for all parallel tasks
        # Merge results into unified structure
        pass

    def quality_evaluation(self):
        """Assess if synthesis is sufficient"""
        # Check coverage of all expected areas
        # Identify gaps
        # Decide if re-delegation needed
        pass

    def error_handling(self):
        """Handle subagent failures gracefully"""
        # If search fails, proceed with analysis
        # Propagate partial results
        # Escalate if critical failures
        pass
```

### 2. **Subagent Characteristics**

```python
class SubagentCharacteristics:
    """What makes a good subagent"""

    def specialized_role(self):
        """Each subagent has ONE clear responsibility"""
        # Search agent: find sources
        # Analysis agent: extract insights
        # Synthesis agent: combine findings
        # NOT: "do whatever needed"
        pass

    def clear_system_prompt(self):
        """Define role, input, output explicitly"""
        system_prompt = """You are a search specialist.

Input: Research topic and subtopics
Output: JSON array of sources with key points
Format: [{source, url, key_points}, ...]"""
        pass

    def isolated_context(self):
        """Don't inherit coordinator's context"""
        # Coordinator's other findings NOT automatically available
        # Must be explicitly passed in prompt
        pass

    def defined_input_output(self):
        """Clear input/output contracts"""
        # Input: topic, search keywords, prior results
        # Output: structured data (JSON)
        # Not: natural language rambling
        pass
```

### 3. **Communication Pattern**

```
Coordinator Decision Tree:

1. RECEIVE user request
   ↓
2. CREATE decomposition plan
   - What subtasks needed?
   - Which subagents? (dynamic selection)
   ↓
3. SPAWN subagents (PARALLEL)
   - Pass explicit context
   - Each gets complete information needed
   ↓
4. RECEIVE subagent results
   - Combine findings
   - Check quality
   ↓
5. EVALUATE synthesis
   - Coverage complete?
   - Gaps identified?
   ↓
6A. IF gaps exist: RE-DELEGATE
    - Call specific subagents
    - Pass prior findings + gap info
    - Re-run synthesis
    ↓
6B. IF complete: RETURN final report
```

---

## ⚠️ ANTI-PATTERNS: What NOT To Do

### Anti-Pattern 1: Direct Subagent Communication

```python
# ❌ WRONG
search_agent.call_analysis_agent()  # No!

# ✅ CORRECT
coordinator.passes_search_results_to_analysis_agent()
```

**Why it's wrong:**

- Breaks observability (coordinator doesn't know what happened)
- Makes debugging impossible
- Violates separation of concerns

---

### Anti-Pattern 2: Assuming Context Inheritance

```python
# ❌ WRONG
synthesis_agent = create_agent(
    # Assumes synthesis_agent automatically knows about search/analysis?
    prompt="Synthesize the findings"
)

# ✅ CORRECT
synthesis_agent = create_agent(
    prompt=f"""Synthesize findings.

Search Results:
{json.dumps(search_findings)}

Analysis Results:
{json.dumps(analysis_findings)}"""
)
```

**Why it's wrong:**

- Subagent has no way to know what happened in other agents
- Results in confused or invalid responses

---

### Anti-Pattern 3: Overly Broad Task Decomposition

```python
# ❌ WRONG
plan = {
    "subtasks": [
        "Research AI",
        "Research creativity",
        "Research everything",  # Too vague!
    ]
}

# ✅ CORRECT
plan = {
    "subtasks": [
        "Find AI applications in visual arts",
        "Find AI applications in music composition",
        "Find AI applications in creative writing",
        "Find AI applications in film production",  # Specific!
    ]
}
```

**Why it's wrong:**

- Vague subtasks lead to incomplete research
- Subagents don't know what to focus on
- Results in poor coverage of topic

---

### Anti-Pattern 4: Not Evaluating Synthesis Quality

```python
# ❌ WRONG
synthesis = call_synthesis_agent(findings)
return synthesis  # Done!

# ✅ CORRECT
synthesis = call_synthesis_agent(findings)

# Evaluate quality
gaps = identify_gaps(
    synthesis=synthesis,
    expected_areas=["Visual Arts", "Music", "Writing", "Film"]
)

if gaps:
    # Re-delegate to fill gaps
    additional_findings = call_search_agent(gaps)
    synthesis = call_synthesis_agent(findings + additional_findings)

return synthesis
```

**Why it's wrong:**

- Doesn't ensure research completeness
- May return partial or insufficient results

---

### Anti-Pattern 5: Arbitrary Tool Restrictions

```python
# ❌ WRONG
# Synthesis agent given search tools
synthesis_agent.tools = ["web_search", "file_read", "api_call"]
# Why would synthesis agent need web_search? It should synthesize!

# ✅ CORRECT
# Each agent given ONLY tools for its role
synthesis_agent.tools = ["format_output", "validate_json"]
search_agent.tools = ["web_search", "cache_results"]
```

**Why it's wrong:**

- Agents tempted to do work outside their specialization
- Results in lower quality overall

---

## 🎓 EXAMPLE SCENARIOS

### Scenario 1: Simple Research Task

**Request:** "What are recent AI developments?"

```
Coordinator Plan:
├─ Search Agent: Find latest news
├─ Analysis Agent: Analyze technical details
└─ Synthesis Agent: Create summary

Execution:
├─ Search finds: 10 articles from 2024–2025
├─ Analysis identifies: 5 key technical advances
└─ Synthesis creates: Organized summary with sources

Gaps Check: All time periods covered? ✓ Complete
Final Result: Return summary
```

---

### Scenario 2: Complex Research Task

**Request:** "Compare AI impacts across 4 creative industries, identify consensus and disagreements"

```
Coordinator Plan:
├─ Search Agent 1: Visual Arts impact
├─ Search Agent 2: Music impact
├─ Search Agent 3: Writing impact
├─ Search Agent 4: Film impact
├─ Analysis Agent: Extract claims from all sources
└─ Synthesis Agent: Compare and identify patterns

Execution (PARALLEL):
├─ All search agents run simultaneously
├─ Analysis agent receives 40+ articles
└─ Synthesis agent receives: 40+ articles + 100+ extracted claims

Gaps Check:
├─ All 4 industries covered? ✓ Yes
├─ Consensus identified? ✓ Yes (10 points of agreement)
├─ Disagreements found? ✓ Yes (5 areas of contention)
└─ Complete? ✓ Yes

Final Result: Comprehensive comparison report
```

---

### Scenario 3: Adaptive Research (With Re-delegation)

**Request:** "Research AI's impact on creativity"

```
Round 1:
├─ Search Agent: Finds articles
├─ Analysis Agent: Extracts insights
└─ Synthesis Agent: Creates draft report

Coverage Evaluation:
├─ Visual Arts: Well covered ✓
├─ Music: Well covered ✓
├─ Writing: Minimal coverage ✗
└─ Film: Missing entirely ✗

Gaps Identified: Writing (partial), Film (none)

Round 2 (Re-delegation):
├─ Search Agent RE-CALLED: Focus on Writing & Film
│   └─ Receives: Prior findings + gap info
├─ Analysis Agent RE-CALLED: Analyze new sources
├─ Synthesis Agent RE-CALLED: Final synthesis

Final Report: All areas covered
```

---

## 📝 SELF-ASSESSMENT QUESTIONS

### Concept Understanding

**Q1: Hub-and-Spoke Pattern**
What's the difference between "hub" (coordinator) and "spokes" (subagents)?

```
Your Answer:
_________________________________________________________________
_________________________________________________________________
Confidence: [ ] Low  [ ] Medium  [ ] High
```

**Q2: Context Passing**
Why can't subagents inherit coordinator's context automatically? How do you pass it?

```
Your Answer:
_________________________________________________________________
_________________________________________________________________
Confidence: [ ] Low  [ ] Medium  [ ] High
```

**Чому subagents НЕ можуть автоматично наслідувати координаторів контекст?**

**1. Архітектурна причина:**

Коли ти викликаєш нового subagent через Claude API, це **новий, окремий API запит**.

```python
# COORDINATOR
coordinator_context = {
    "search_findings": [article1, article2, ...],
    "analysis_results": [insight1, insight2, ...],
}

# Потім потім спавниш НОВОГО subagent
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[...]  # ← НОВИЙ message history!
    # Coordinator's context НЕ передається автоматично!
)
```

**Ключовий момент:** Кожен API запит — це **окремий контекст**. Claude не знає про попередні запити координатора.

**2. Аналогія з реальним світом:**

```
❌ Неправильна аналогія:
Ти розказуєш Петру деталі про проект.
Потім петро розказує Марії.
"Марія вже знає, що ти розказав Петру"
→ НЕПРАВИЛЬНО! Марія не чула ту розмову!

✅ Правильна аналогія:
Ти розказуєш Петру: "Знайди інформацію про продажі"
Петро знаходить: "У 2024 році було 1000 продажів"
Потім ти кажеш Марії: "Синтезуй це"
Але тобі потрібно розказати Марії: "Петро знайшов, що було 1000 продажів"
→ ПРАВИЛЬНО! Явно передав контекст
```

#### **3. Технічна причина:**

```python
# НЕПРАВИЛЬНО (не працює):
class SubagentA:
    def execute(self):
        result_a = "...findings..."
        return result_a

class SubagentB:
    def execute(self):
        # SubagentB НЕ знає про result_a!
        # Немає механізму автоматичної передачі
        return f"Based on {result_a}"  # ← ПОМИЛКА!

# ПРАВИЛЬНО (працює):
coordinator_findings = SubagentA.execute()  # result_a

context_for_b = f"""
Prior findings from SubagentA:
{coordinator_findings}

Your task: Synthesize these findings
"""

SubagentB.execute(prompt=context_for_b)  # Явно передав
```

---

**Як правильно передавати контекст?**

**Метод 1: Явна передача в prompt**

```python
# ПРАВИЛЬНО ✅

# Step 1: Coordinator отримав результати від SubagentA
search_results = [
    {
        "url": "article1.com",
        "title": "AI Advances in 2024",
        "key_points": ["Point 1", "Point 2"]
    },
    {
        "url": "article2.com",
        "title": "AI Ethics Debate",
        "key_points": ["Point A", "Point B"]
    }
]

# Step 2: Coordinator передає це SubagentB ЯВНО в prompt
synthesis_prompt = f"""
You are a synthesis agent.

Your task: Create a comprehensive report.

CONTEXT FROM PRIOR AGENT (SubagentA - Search):
Here are the search results found:

{json.dumps(search_results, indent=2)}

Analyze these results and identify:
1. Common themes
2. Areas of disagreement
3. Coverage gaps

Format your response as JSON with fields: themes, disagreements, gaps
"""

# Step 3: Викличи SubagentB з цим контекстом
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[
        {"role": "user", "content": synthesis_prompt}
    ]
)
```

**Метод 2: Структурований контекст блок**

```python
# КРАЩЕ ПРАКТИКА ✅

def create_synthesis_prompt(
    search_results: List[Dict],
    analysis_results: List[Dict],
    expected_coverage: List[str]
) -> str:
    """Create explicit context block for synthesis agent"""

    return f"""
You are a research synthesis specialist.

════════════════════════════════════════════════════════════
COORDINATOR CONTEXT (Required for synthesis)
════════════════════════════════════════════════════════════

SEARCH RESULTS (from Web Search SubAgent):
Number of sources found: {len(search_results)}

{json.dumps(search_results, indent=2)}

---

ANALYSIS RESULTS (from Document Analysis SubAgent):
Number of documents analyzed: {len(analysis_results)}

{json.dumps(analysis_results, indent=2)}

---

EXPECTED COVERAGE AREAS (per Coordinator's plan):
{', '.join(expected_coverage)}

════════════════════════════════════════════════════════════
YOUR TASK
════════════════════════════════════════════════════════════

1. Synthesize findings from BOTH prior agents
2. For each expected coverage area:
   - Is it well-covered? (multiple sources agree)
   - Is it contested? (sources disagree)
   - Is it missing? (no sources found)

3. Return JSON:
{{
    "synthesis": "Narrative combining all findings...",
    "coverage_assessment": {{
        "well_covered": ["area1", "area2"],
        "contested": ["area3"],
        "missing": ["area4"]
    }},
    "gaps_to_fill": ["missing_area4"]
}}
"""
```

**Метод 3: Metadata Preservation**

```python
# Коли передаєш контекст, збережи METADATA ✅

# НЕПРАВИЛЬНО ❌
search_findings = [
    "AI enables new art forms",
    "Job displacement concerns",
    "Tools democratize creativity"
]

# Synthesis agent не знає:
# - Звідки ці знахідки?
# - Наскільки вони надійні?
# - Які джерела?

# ПРАВИЛЬНО ✅
search_findings = [
    {
        "finding": "AI enables new art forms",
        "source": "MIT Technology Review",
        "url": "https://...",
        "date": "2024-11-15",
        "credibility": 0.95,
        "direct_quote": "\"AI has opened entirely new possibilities...\""
    },
    {
        "finding": "Job displacement concerns",
        "source": "Forbes Interview",
        "url": "https://...",
        "date": "2024-10-20",
        "credibility": 0.80,
        "context": "According to artist interviews"
    }
]

# Тепер synthesis agent може:
# - Бачити джерела
# - Оцінити надійність
# - Цитувати оригінали
```

---

**Повна Архітектура Context Passing:**

```
┌──────────────────────────────────────┐
│      USER REQUEST                    │
│  "Research AI in creativity"         │
└──────────────────┬────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  COORDINATOR                         │
│  1. Parse request                    │
│  2. Create plan: {subtasks: [...]}   │
│  3. Determine subagents needed       │
└──────────────────┬────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
   ┌────────────────┐  ┌────────────────┐
   │  SEARCH AGENT  │  │ ANALYSIS AGENT │
   │  (new context) │  │  (new context) │
   │                │  │                │
   │ Input prompt:  │  │ Input prompt:  │
   │ "Find articles"│  │ "Analyze docs" │
   │                │  │                │
   │ Output:        │  │ Output:        │
   │ search_results │  │analysis_results│
   └────────┬───────┘  └────────┬───────┘
            │                   │
            └───────────┬───────┘
                        │
    ┌───────────────────▼────────────────────┐
    │  COORDINATOR (Aggregates Results)      │
    │  Receives:                             │
    │  - search_results from SearchAgent     │
    │  - analysis_results from AnalysisAgent│
    │  Evaluates: Are findings complete?    │
    │             Are there gaps?           │
    └────────────────┬──────────────────────┘
                     │
        ┌────────────▼────────────┐
        │                         │
        │ Gaps found?  YES/NO     │
        │                         │
        └─YES────────┬────────NO──┘
                     │
              ┌──────┴──────┐
              │             │
              ▼             ▼
        [RE-DELEGATE]  ┌─────────────┐
        (Search or     │SYNTHESIS    │
         Analysis      │AGENT        │
         again)        │(new context)│
                       │             │
                       │Input:       │
                       │ - search+   │
                       │ - analysis+ │
                       │ - plan      │
                       │             │
                       │Output:      │
                       │ final_report│
                       └─────────────┘
                             │
                             ▼
                      ┌──────────────┐
                      │  FINAL       │
                      │  REPORT      │
                      │  (to user)   │
                      └──────────────┘

KEY POINTS:
• Кожен субагент отримує НОВИЙ контекст
• Контекст передається явно у prompt
• Metadata зберігаються для трасування
• Координатор контролює усі передачі
```

---

```python
import anthropic
import json

client = anthropic.Anthropic()

# Step 1: SEARCH AGENT (new context)
search_prompt = """Find 3 articles about AI in music composition.
Return JSON: [{title, url, key_points}]"""

search_response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[{"role": "user", "content": search_prompt}]
)
search_results = json.loads(search_response.content[0].text)

print(f"Search Agent found: {search_results}")

# Step 2: ANALYSIS AGENT (new context, but with search results passed explicitly)
analysis_prompt = f"""
Analyze these music composition articles:

{json.dumps(search_results, indent=2)}

Extract: [claims, credibility_scores, evidence]
Return JSON."""

analysis_response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[{"role": "user", "content": analysis_prompt}]
)
analysis_results = json.loads(analysis_response.content[0].text)

print(f"Analysis Agent found: {analysis_results}")

# Step 3: SYNTHESIS AGENT (new context, with BOTH prior results)
synthesis_prompt = f"""
You are synthesis specialist.

SEARCH FINDINGS (from SearchAgent):
{json.dumps(search_results, indent=2)}

ANALYSIS FINDINGS (from AnalysisAgent):
{json.dumps(analysis_results, indent=2)}

Create comprehensive report combining both.
Identify: agreements, disagreements, gaps."""

synthesis_response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[{"role": "user", "content": synthesis_prompt}]
)
final_report = synthesis_response.content[0].text

print(f"\nFinal Report:\n{final_report}")

# KEY LEARNING:
# ✅ SearchAgent didn't know about AnalysisAgent (isolated)
# ✅ AnalysisAgent received search results explicitly
# ✅ SynthesisAgent received BOTH search AND analysis
# ✅ All context was EXPLICIT in prompts
# ✅ No automatic inheritance happened
```

---

Порівняльна Таблиця:

| Аспект                 | Неправильно ❌                        | Правильно ✅                       |
| ---------------------- | ------------------------------------- | ---------------------------------- |
| **Context sharing**    | Припускаю автоматичне                 | Передаю явно у prompt              |
| **Metadata**           | Не зберігаю                           | Зберігаю (URL, дата, джерело)      |
| **Coordinator role**   | Спостерігач                           | Активно керує (передає контекст)   |
| **Subagent isolation** | Намагаюсь їх з'єднати                 | Ізоліую їх, контролюю зв'язок      |
| **Error handling**     | Якщо контекст не передався - проблема | Перевіряю контекст перед передачею |
| **Scalability**        | Складно додати 4-й агент              | Просто додаю в coordinator loop    |

---

```markdown
**Q2: Context Passing**

Субагенти НЕ можуть автоматично наслідувати контекст координатора, бо:

1. **Архітектурна причина:** Кожен API запит до Claude — це окремий контекст.
   Claude не знає про попередні запити координатора. Це як телефонувати
   новій людині — вона не чула твоєї попередної розмови.

2. **Технічна причина:** Немає механізму автоматичної передачі контексту
   між відокремленими API запитами. Кожен субагент отримує НОВИЙ, пустий
   message history.

Як передавати контекст ПРАВИЛЬНО:

✅ **Метод 1 — Явна передача в prompt:**
Всі результати від попередніх субагентів включаю у prompt як JSON.

synthesis_prompt = f"""
SEARCH RESULTS (від SubAgentA):
{json.dumps(search_results)}

ANALYSIS RESULTS (від SubAgentB):
{json.dumps(analysis_results)}

Your task: Synthesize these...
"""

✅ **Метод 2 — Структурований контекст блок:**
Організую контекст в зрозумілі секції:

- COORDINATOR CONTEXT (заголовок)
- Search findings (з метаданими)
- Analysis findings (з джерелами)
- Expected coverage areas
- YOUR TASK (що робити)

✅ **Метод 3 — Збереження Metadata:**
Не просто передаю дані, але й зберігаю:

- Джерела (URL, назва)
- Дати публікації
- Оцінка надійності
- Прямі цитати

Це дозволяє synthesis агенту:

- Перевірити надійність
- Цитувати оригіналу
- Об'єднати знахідки правильно

**Ключовий момент:**
Координатор активно керує потоком даних. Субагенти повністю ізольовані один від
одного. Весь контекст передається явно. Це забезпечує надійність,
спостереженність і масштабованість.

Confidence: [X] High
```

**Q3: Dynamic Selection**
How does coordinator decide which subagents to invoke? Is it always all of them?

```
Your Answer:
_________________________________________________________________
_________________________________________________________________
Confidence: [ ] Low  [ ] Medium  [ ] High
```

**Q4: Iterative Refinement**
How does coordinator evaluate if synthesis is complete? What triggers re-delegation?

```
Your Answer:
_________________________________________________________________
_________________________________________________________________
Confidence: [ ] Low  [ ] Medium  [ ] High
```

**Q5: Parallel vs Sequential**
When should subagents run in parallel? What are the trade-offs?

```
Your Answer:
_________________________________________________________________
_________________________________________________________________
Confidence: [ ] Low  [ ] Medium  [ ] High
```

---

## 🧪 PRACTICE EXERCISE 1: Design Multi-Agent System (On Paper)

**Scenario:** You're building a research system for "Impact of Climate Change on World Economy"

**Your Task:** Create a coordinator and subagent design

### Part 1: Define Coordinator

```
Coordinator Responsibilities:
1. _________________________________________________
2. _________________________________________________
3. _________________________________________________
4. _________________________________________________
5. _________________________________________________

How will coordinator decide which subagents to call?
_____________________________________________________
_____________________________________________________

How will coordinator evaluate synthesis quality?
_____________________________________________________
_____________________________________________________
```

### Part 2: Design Subagents

```
Subagent 1:
├─ Name: _______________________________________________
├─ Specialization: _____________________________________
├─ System Prompt (1 sentence): _________________________
├─ Input it receives: _________________________________
└─ Output it produces: ________________________________

Subagent 2:
├─ Name: _______________________________________________
├─ Specialization: _____________________________________
├─ System Prompt (1 sentence): _________________________
├─ Input it receives: _________________________________
└─ Output it produces: ________________________________

Subagent 3:
├─ Name: _______________________________________________
├─ Specialization: _____________________________________
├─ System Prompt (1 sentence): _________________________
├─ Input it receives: _________________________________
└─ Output it produces: ________________________________
```

### Part 3: Communication Flow

```
Draw the communication flow:

User Request
    ↓
[Coordinator]
    │
    ├─→ [Subagent 1] ──→ Result A
    │
    ├─→ [Subagent 2] ──→ Result B
    │
    └─→ [Subagent 3] ──→ Result C
        (or sequential?)

    [Coordinator Evaluation]

    Gaps? YES/NO
    If YES, re-delegate: _______________

Final Report
```

---

## 💻 PRACTICE EXERCISE 2: Implement Multi-Agent System (Code)

### Part 1: Set Up Project

```bash
mkdir domain-1-2-orchestration
cd domain-1-2-orchestration
python -m venv venv
source venv/bin/activate
pip install anthropic
```

### Part 2: Create `orchestrator.py`

**Template:**

```python
import anthropic
import json
from typing import List, Dict

client = anthropic.Anthropic(api_key="your-key")

class MultiAgentOrchestrator:
    """TODO: Implement this class"""

    def __init__(self):
        self.findings = {}

    def run_research(self, topic: str):
        """TODO: Implement coordinator loop"""

        # Step 1: Create decomposition plan
        plan = self._create_plan(topic)
        print(f"Plan: {plan}")

        # Step 2: Spawn subagents
        # TODO: Implement parallel execution

        # Step 3: Coordinator evaluation
        # TODO: Implement synthesis quality check

        # Step 4: If gaps, re-delegate
        # TODO: Implement re-delegation

        pass

    def _create_plan(self, topic: str) -> Dict:
        """TODO: Use Claude to create decomposition plan"""
        pass

    def _spawn_search_agent(self, topic: str):
        """TODO: Implement search subagent"""
        pass

    def _spawn_analysis_agent(self, findings: List):
        """TODO: Implement analysis subagent"""
        pass

    def _spawn_synthesis_agent(self, findings: Dict):
        """TODO: Implement synthesis subagent"""
        pass

    def _identify_gaps(self, synthesis: str):
        """TODO: Identify coverage gaps"""
        pass


if __name__ == "__main__":
    orchestrator = MultiAgentOrchestrator()

    # TODO: Test with different topics
    result = orchestrator.run_research(
        "Impact of AI on creative industries"
    )

    print("\nFinal Report:")
    print(json.dumps(result, indent=2))
```

### Part 3: Implementation Steps

1. **Implement `_create_plan()`:**

   - Use Claude to analyze topic
   - Return: {main_topic, subtasks}

2. **Implement `_spawn_search_agent()`:**

   - Create prompt for search agent
   - Return: [sources with URLs and key points]

3. **Implement `_spawn_analysis_agent()`:**

   - Pass search results explicitly
   - Return: [analyzed findings]

4. **Implement `_spawn_synthesis_agent()`:**

   - Pass BOTH search and analysis results
   - Return: {synthesis, gaps, contested_areas}

5. **Implement `_identify_gaps()`:**

   - Compare synthesis against expected subtasks
   - Return: list of missing areas

6. **Implement `run_research()`:**
   - Coordinator loop: plan → spawn → evaluate → re-delegate if needed
   - Handle both sequential and parallel execution

### Part 4: Testing

```python
# Test cases to implement:

# Test 1: Simple topic
orchestrator.run_research("What is machine learning?")

# Test 2: Complex topic with multiple subtasks
orchestrator.run_research(
    "Compare impacts of AI on visual arts, music, and film"
)

# Test 3: Topic likely to have gaps
orchestrator.run_research(
    "Emerging ethical issues in AI-assisted creativity"
)

# For each test:
# ✅ Verify all subagents were called
# ✅ Verify context passed correctly
# ✅ Verify synthesis evaluated for gaps
# ✅ Verify re-delegation triggered if needed
```

---

## ✅ COMPLETION CHECKLIST

Before moving to 1.3, verify you understand:

### Conceptual Understanding

- [ ] Can explain hub-and-spoke pattern in simple terms
- [ ] Can explain why subagents have isolated context
- [ ] Can describe coordinator's 5 main responsibilities
- [ ] Understand when subagents run parallel vs sequential
- [ ] Understand anti-patterns and why they're wrong

### Practical Skills

- [ ] Can design multi-agent system for given problem
- [ ] Can write system prompts for different subagents
- [ ] Can structure explicit context passing in prompts
- [ ] Can implement coordinator evaluation logic
- [ ] Can handle re-delegation and iterative refinement

### Code Implementation

- [ ] Completed Exercise 1 (design on paper)
- [ ] Completed Exercise 2 (full Python implementation)
- [ ] Tested with 3+ different topics
- [ ] All subagents working correctly
- [ ] Context passing verified
- [ ] Re-delegation working as expected

### Self-Assessment

- [ ] All 5 self-assessment questions answered
- [ ] Confidence level 8+ / 10 on all questions
- [ ] Can explain to someone else

---

## 🔗 NEXT STEPS

Once this is mastered, move to **Domain 1.3: Subagent Invocation, Context Passing, Spawning**.

Key Takeaway:

> **Hub-and-Spoke is about specialization and clarity.** The coordinator knows the big picture. Each subagent focuses on one job. Context is always explicit. That's how you build reliable multi-agent systems.

---

## 📚 Reference: Key Patterns

### Pattern 1: Explicit Context Passing

```python
# Always include all needed context in prompt
prompt = f"""
CONTEXT FROM COORDINATOR:
- Prior search findings: {search_findings}
- Analysis results: {analysis_findings}
- Expected coverage areas: {expected_areas}

YOUR TASK: Synthesize these into a report
"""
```

### Pattern 2: Result Aggregation

```python
# Collect all results before proceeding
results = {
    "search": search_agent_output,
    "analysis": analysis_agent_output,
    "synthesis": synthesis_agent_output,
}

# Evaluate completeness
gaps = evaluate_gaps(results)

# If gaps exist, re-run
if gaps:
    results = re_run_with_gap_focus(gaps)
```

### Pattern 3: Error Propagation

```python
# If one subagent fails, proceed with partial results
try:
    search_results = call_search_agent()
except SearchError:
    search_results = []  # Continue with empty

# Propagate to synthesis
synthesis = call_synthesis_agent(
    search_results=search_results,  # Might be empty
    analysis_results=analysis_results,
)

# Annotate gaps in final output
synthesis["gaps"].append("Search unavailable")
```

---

**Good luck! You've got this.** 🚀
