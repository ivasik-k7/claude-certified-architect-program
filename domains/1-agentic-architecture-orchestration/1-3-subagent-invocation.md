# Sub-Agent Invocation: Complete Methodological Guide

**Table of Contents**

1. [Fundamentals](#fundamentals)
2. [Architecture Patterns](#architecture-patterns)
3. [Implementation Strategies](#implementation-strategies)
4. [Code Examples](#code-examples)
5. [Best Practices](#best-practices)
6. [Advanced Patterns](#advanced-patterns)
7. [Troubleshooting & Monitoring](#troubleshooting--monitoring)
8. [Real-World Use Cases](#real-world-use-cases)

---

## Fundamentals

### What is Sub-Agent Invocation?

**Definition:** Sub-agent invocation is a pattern where a primary/parent agent delegates specific tasks to specialized child agents rather than using generic tools. Each sub-agent is an agentic loop in its own right, with its own reasoning, tools, and decision-making capabilities.

**Why NOT Just Use Tools?**

| Aspect             | Generic Tools                   | Sub-Agents                      |
| ------------------ | ------------------------------- | ------------------------------- |
| **Reasoning**      | None — execute blindly          | Full reasoning loop             |
| **Autonomy**       | None — agent decides everything | Can make independent decisions  |
| **Specialization** | General purpose                 | Domain-specific                 |
| **Error Handling** | Caller handles                  | Agent handles internally        |
| **Iteration**      | Single execution                | Can iterate until goal achieved |
| **Context**        | Flat                            | Rich, hierarchical              |
| **Cost**           | Lower per call                  | Higher (agent overhead)         |

**Example Comparison:**

```
❌ TOOL-BASED APPROACH:
Main Agent → web_search() → fetch() → analyze() → summarize() → done
(Everything in one loop, main agent makes all decisions)

✅ SUB-AGENT APPROACH:
Main Agent → Research Sub-Agent (independent loop) → Synthesis
                ↓
           (autonomously: search, fetch, verify, refine)

(Research sub-agent runs its own agentic loop, returns polished result)
```

---

### Core Concepts

#### 1. **Agent Roles & Responsibilities**

Each sub-agent should have a clearly defined charter:

```
Research Agent:
├─ Goal: Find and validate information from multiple sources
├─ Tools: web_search, fetch, database_query
├─ Success Criteria: 3+ sources, cross-validated facts
└─ Constraints: Max 5 iterations, max 2000 tokens

Synthesis Agent:
├─ Goal: Combine multiple inputs into cohesive narrative
├─ Tools: text_processing, formatting, grammar_check
├─ Success Criteria: Well-structured, clear, <1000 words
└─ Constraints: Max 3 iterations, no external tools

Validation Agent:
├─ Goal: Check output quality, factual accuracy
├─ Tools: fact_checker, schema_validator, linter
├─ Success Criteria: All checks pass, 0 errors
└─ Constraints: Max 2 iterations, deterministic checks
```

#### 2. **Context & State Management**

Sub-agents inherit context but work autonomously:

```javascript
// Parent Agent's Context
const parentContext = {
  originalRequest: "Research AWS EKS best practices and write a guide",
  userPreferences: { targetAudience: "devops-engineers", depth: "advanced" },
  constraints: { maxTokens: 5000, maxTime: 60000 },
  previousResults: [], // Accumulate results from sub-agents
};

// Research Sub-Agent Gets:
const researchContext = {
  ...parentContext, // Inherits everything
  role: "research",
  goal: "Find 5+ authoritative sources on EKS best practices",
  tools: ["web_search", "fetch", "validate_source"],
  // Research agent can modify but parent gets result
};

// Synthesis Sub-Agent Gets:
const synthesisContext = {
  ...parentContext,
  role: "synthesis",
  goal: "Write guide from research results",
  input: {
    researchFindings: results_from_research_agent,
    targetLength: 2000,
    style: "technical but accessible",
  },
};
```

---

## Architecture Patterns

### Pattern 1: Sequential Delegation

**Use Case:** Tasks have clear dependencies (A must finish before B starts)

**Structure:**

```
Parent Agent
    ↓
Sub-Agent A (complete)
    ↓
Sub-Agent B (uses A's output)
    ↓
Sub-Agent C (uses B's output)
    ↓
Parent Agent combines results
```

**Pros:**

- Simple to understand & debug
- Guaranteed ordering
- Easy error handling (stop at first failure)

**Cons:**

- Slower (no parallelization)
- Later agents block on earlier ones

**Implementation:**

```javascript
async function sequentialDelegation(userPrompt, context) {
  try {
    // Step 1: Research
    const researchResult = await invokeSubAgent("research", {
      prompt: userPrompt,
      context,
      maxIterations: 5,
    });

    if (!researchResult.success) {
      return { error: "Research failed", details: researchResult };
    }

    // Step 2: Synthesis (depends on research)
    const synthesisResult = await invokeSubAgent("synthesis", {
      researchData: researchResult.output,
      context,
      maxIterations: 3,
    });

    // Step 3: Validation (depends on synthesis)
    const validationResult = await invokeSubAgent("validation", {
      content: synthesisResult.output,
      context,
      maxIterations: 2,
    });

    return {
      success: true,
      output: synthesisResult.output,
      metadata: {
        research: researchResult.metrics,
        synthesis: synthesisResult.metrics,
        validation: validationResult.metrics,
      },
    };
  } catch (error) {
    return { error: error.message, stack: error.stack };
  }
}
```

### Pattern 2: Parallel Delegation

**Use Case:** Multiple independent tasks can run simultaneously

**Structure:**

```
Parent Agent
    ├─ Sub-Agent A (research)
    ├─ Sub-Agent B (analysis)
    └─ Sub-Agent C (benchmarking)
         ↓
    All complete → Parent combines results
```

**Pros:**

- Much faster (concurrent execution)
- Independent sub-agents don't block each other
- Better resource utilization

**Cons:**

- More complex orchestration
- Harder error handling (partial failures)
- Need to merge results

**Implementation:**

```javascript
async function parallelDelegation(userPrompt, context) {
  try {
    // Launch all sub-agents concurrently
    const [researchResult, analysisResult, benchmarkResult] = await Promise.all(
      [
        invokeSubAgent("research", {
          prompt: userPrompt,
          focus: "information gathering",
          context,
        }),
        invokeSubAgent("analysis", {
          prompt: userPrompt,
          focus: "architecture analysis",
          context,
        }),
        invokeSubAgent("benchmark", {
          prompt: userPrompt,
          focus: "performance metrics",
          context,
        }),
      ]
    );

    // Handle partial failures
    const results = [researchResult, analysisResult, benchmarkResult]
      .filter((r) => r.success)
      .map((r) => r.output);

    if (results.length === 0) {
      return { error: "All sub-agents failed" };
    }

    // Combine results (synthesis sub-agent or parent logic)
    const synthesis = await invokeSubAgent("synthesis", {
      inputs: results,
      context,
    });

    return {
      success: true,
      output: synthesis.output,
      completedAgents: results.length,
      failedAgents: 3 - results.length,
    };
  } catch (error) {
    return { error: error.message };
  }
}
```

### Pattern 3: Conditional Delegation

**Use Case:** Different paths based on input type or conditions

**Structure:**

```
Parent Agent (evaluates condition)
    ├─ IF type="technical"
    │   └─ Technical Sub-Agent
    ├─ ELSE IF type="business"
    │   └─ Business Sub-Agent
    └─ ELSE
        └─ General Sub-Agent
```

**Implementation:**

```javascript
async function conditionalDelegation(userPrompt, context) {
  // Parent agent analyzes request type
  const requestAnalysis = await analyzeRequest(userPrompt);

  let subAgentType, subAgentConfig;

  if (requestAnalysis.isDeepTechnical) {
    subAgentType = "technical-deep-dive";
    subAgentConfig = {
      maxIterations: 10,
      focusAreas: ["architecture", "implementation", "optimization"],
      tools: ["code-executor", "performance-analyzer"],
    };
  } else if (requestAnalysis.isBusiness) {
    subAgentType = "business-analyst";
    subAgentConfig = {
      maxIterations: 5,
      focusAreas: ["roi", "market-fit", "competitive-analysis"],
      tools: ["market-data", "financial-calculator"],
    };
  } else {
    subAgentType = "general";
    subAgentConfig = {
      maxIterations: 3,
      focusAreas: ["overview"],
      tools: ["web-search", "summarizer"],
    };
  }

  const result = await invokeSubAgent(subAgentType, {
    prompt: userPrompt,
    config: subAgentConfig,
    context,
  });

  return result;
}
```

### Pattern 4: Hierarchical Delegation (Tree of Agents)

**Use Case:** Complex problems requiring multiple levels of specialization

**Structure:**

```
                Main Agent
                    │
        ┌───────────┼───────────┐
        │           │           │
    Research    Analysis    Synthesis
    Sub-Agent   Sub-Agent   Sub-Agent
        │
     ┌──┴──┐
     │     │
  Search Validate
  Agent   Agent
```

**Implementation:**

```javascript
async function hierarchicalDelegation(userPrompt, context) {
  // Level 1: Main Agent delegates to 3 domain sub-agents
  const [research, analysis] = await Promise.all([
    // Research sub-agent can invoke its own children
    invokeSubAgent("research", {
      prompt: userPrompt,
      context,
      canInvokeChildren: true,
      children: ["search-agent", "validation-agent"],
    }),
    invokeSubAgent("analysis", {
      prompt: userPrompt,
      context,
      maxIterations: 5,
    }),
  ]);

  // Level 2: Synthesis uses results from above
  const synthesis = await invokeSubAgent("synthesis", {
    researchData: research.output,
    analysisData: analysis.output,
    context,
  });

  return {
    success: true,
    output: synthesis.output,
    tree: {
      main: { status: "complete" },
      level1: {
        research: research.status,
        analysis: analysis.status,
      },
      level2: {
        synthesis: synthesis.status,
      },
    },
  };
}
```

---

## Implementation Strategies

### Strategy 1: API-Based Sub-Agent Invocation

**How it works:**

- Each sub-agent is a separate API endpoint
- Parent agent makes HTTP calls to invoke them
- Clean separation of concerns
- Easier to scale

**Pros:**

- Distributed system (can scale independently)
- Language agnostic
- Easy to monitor/debug (network calls)
- Can use different models/configs per agent

**Cons:**

- Network latency
- More complex error handling
- Harder to share state
- Cost of API calls

**Implementation:**

```javascript
async function invokeSubAgentViaAPI(subAgentName, payload) {
  const apiUrl = `https://agents.internal.company.com/${subAgentName}/invoke`;

  const startTime = Date.now();

  try {
    const response = await fetch(apiUrl, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "X-Agent-Id": process.env.AGENT_ID,
        "X-Request-Id": generateRequestId(),
      },
      body: JSON.stringify({
        ...payload,
        parentAgentId: process.env.AGENT_ID,
        timestamp: Date.now(),
      }),
      timeout: 60000, // 60 second timeout
    });

    if (!response.ok) {
      throw new Error(
        `Sub-agent returned ${response.status}: ${response.statusText}`
      );
    }

    const result = await response.json();
    const duration = Date.now() - startTime;

    // Log metrics
    logMetrics({
      subAgent: subAgentName,
      status: "success",
      duration,
      tokensUsed: result.metrics?.tokensUsed,
      iterations: result.metrics?.iterations,
    });

    return {
      success: true,
      output: result.output,
      metrics: result.metrics,
    };
  } catch (error) {
    const duration = Date.now() - startTime;

    logMetrics({
      subAgent: subAgentName,
      status: "error",
      duration,
      error: error.message,
    });

    // Retry logic (optional)
    if (shouldRetry(error) && payload.retryCount < 3) {
      return invokeSubAgentViaAPI(subAgentName, {
        ...payload,
        retryCount: (payload.retryCount || 0) + 1,
      });
    }

    throw error;
  }
}
```

### Strategy 2: Function-Based Sub-Agent Invocation

**How it works:**

- Sub-agents are functions in same process
- Direct function calls (no network)
- Shared state/memory

**Pros:**

- Fast (no network latency)
- Simple debugging
- Easy state sharing
- Lower latency

**Cons:**

- Coupled implementation
- Harder to scale
- Resource contention
- Crashes affect entire system

**Implementation:**

```javascript
// Each sub-agent is a function that returns an agentic loop
class SubAgent {
  constructor(name, systemPrompt, tools, config = {}) {
    this.name = name;
    this.systemPrompt = systemPrompt;
    this.tools = tools;
    this.maxIterations = config.maxIterations || 5;
    this.maxTokens = config.maxTokens || 2000;
  }

  async invoke(userPrompt, context = {}) {
    console.log(`[${this.name}] Starting sub-agent invocation`);

    let messages = [
      {
        role: "user",
        content: `${this.systemPrompt}\n\nTask: ${userPrompt}`,
      },
    ];

    let iteration = 0;
    const startTime = Date.now();

    while (iteration < this.maxIterations) {
      // Run agentic loop (same as main loop)
      const response = await this.runLoop(messages);

      messages.push({ role: "assistant", content: response.content });

      if (response.stop_reason === "end_turn") {
        return {
          success: true,
          output: this.extractText(response.content),
          metrics: {
            iterations: iteration + 1,
            duration: Date.now() - startTime,
            tokensUsed: response.tokensUsed,
          },
        };
      }

      if (response.stop_reason === "tool_use") {
        // Execute tool and append result
        const toolResults = await this.executeTools(response.content);
        messages.push({ role: "user", content: toolResults });
        iteration++;
      }
    }

    return {
      success: false,
      error: "Max iterations reached",
      output: this.extractText(messages[messages.length - 1].content),
      metrics: { iterations: iteration },
    };
  }

  async runLoop(messages) {
    return await client.messages.create({
      model: "claude-3-5-sonnet-20241022",
      max_tokens: this.maxTokens,
      tools: this.tools,
      messages: messages,
    });
  }

  async executeTools(contentBlocks) {
    const toolResults = [];

    for (const block of contentBlocks) {
      if (block.type === "tool_use") {
        const result = await this.callTool(block.name, block.input);
        toolResults.push({
          type: "tool_result",
          tool_use_id: block.id,
          content: result,
        });
      }
    }

    return toolResults;
  }

  async callTool(toolName, toolInput) {
    // Find tool and execute
    const tool = this.tools.find((t) => t.name === toolName);
    if (!tool) throw new Error(`Unknown tool: ${toolName}`);

    return await tool.execute(toolInput);
  }

  extractText(contentBlocks) {
    return contentBlocks
      .filter((c) => c.type === "text")
      .map((c) => c.text)
      .join("\n");
  }
}

// Usage
const researchAgent = new SubAgent(
  "research",
  "You are a thorough researcher. Find accurate information from multiple sources.",
  [webSearchTool, fetchTool],
  { maxIterations: 5, maxTokens: 3000 }
);

const result = await researchAgent.invoke("Find AWS EKS best practices");
console.log(result.output);
```

### Strategy 3: Hybrid Approach

**How it works:**

- Local sub-agents for simple tasks (low latency)
- Remote API calls for expensive operations (parallelization)

```javascript
async function hybridDelegation(userPrompt, context) {
  // Local (fast, simple)
  const classification = await classifyRequestLocal(userPrompt);

  // Remote (parallel, complex)
  const [research, analysis] = await Promise.all([
    invokeSubAgentAPI("research-api", { prompt: userPrompt }),
    invokeSubAgentAPI("analysis-api", { prompt: userPrompt }),
  ]);

  // Local (fast, final processing)
  const synthesis = await synthesizeLocal({
    classification,
    research,
    analysis,
  });

  return synthesis;
}
```

---

## Code Examples

### Example 1: Simple Sequential Research Pipeline

```javascript
const Anthropic = require("@anthropic-ai/sdk");

const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});

// Define sub-agents
const AGENTS = {
  research: {
    systemPrompt: `You are a research specialist. Your job is to:
1. Find information about the topic from multiple sources
2. Validate facts against each other
3. Return structured findings with sources`,
    tools: ["web_search", "fetch", "validate_fact"],
  },
  synthesis: {
    systemPrompt: `You are a synthesis specialist. Your job is to:
1. Take research findings
2. Organize them into a coherent narrative
3. Highlight key insights and contradictions`,
    tools: ["text_formatter", "grammar_checker"],
  },
};

// Tool definitions (simplified)
const tools = [
  {
    name: "web_search",
    description: "Search the web",
    input_schema: {
      type: "object",
      properties: {
        query: { type: "string" },
      },
      required: ["query"],
    },
  },
  {
    name: "fetch",
    description: "Fetch content from URL",
    input_schema: {
      type: "object",
      properties: {
        url: { type: "string" },
      },
      required: ["url"],
    },
  },
];

async function invokeSubAgent(agentName, userPrompt, prevResults = null) {
  const agent = AGENTS[agentName];

  // Build system message
  let systemMessage = agent.systemPrompt;
  if (prevResults) {
    systemMessage += `\n\nPrevious research findings:\n${JSON.stringify(
      prevResults,
      null,
      2
    )}`;
  }

  let messages = [{ role: "user", content: userPrompt }];

  console.log(`\n[${agentName}] Starting invocation...`);

  let iteration = 0;
  const MAX_ITERATIONS = 5;

  while (iteration < MAX_ITERATIONS) {
    const response = await client.messages.create({
      model: "claude-3-5-sonnet-20241022",
      max_tokens: 2000,
      system: systemMessage,
      tools: tools.filter((t) => agent.tools.includes(t.name)),
      messages: messages,
    });

    // Append assistant response
    messages.push({ role: "assistant", content: response.content });

    if (response.stop_reason === "end_turn") {
      const text = response.content
        .filter((c) => c.type === "text")
        .map((c) => c.text)
        .join("\n");

      console.log(`[${agentName}] Complete after ${iteration + 1} iterations`);
      return text;
    }

    if (response.stop_reason === "tool_use") {
      // Execute tools
      const toolResults = [];
      for (const block of response.content) {
        if (block.type === "tool_use") {
          const result = await executeTool(block.name, block.input);
          toolResults.push({
            type: "tool_result",
            tool_use_id: block.id,
            content: result,
          });
          console.log(`[${agentName}] Executed tool: ${block.name}`);
        }
      }

      messages.push({ role: "user", content: toolResults });
      iteration++;
    }
  }

  throw new Error(`${agentName} exceeded max iterations`);
}

async function executeTool(toolName, toolInput) {
  // Mock tool execution
  const mocks = {
    web_search: () =>
      JSON.stringify([
        { title: "Best Practices", url: "example.com/1", snippet: "..." },
        { title: "Guide", url: "example.com/2", snippet: "..." },
      ]),
    fetch: () => "Full article content...",
    text_formatter: () => "Formatted text",
  };

  return mocks[toolName]?.() || `Result from ${toolName}`;
}

// Main execution
async function main() {
  try {
    const userQuery = "Research the latest developments in quantum computing";

    // Step 1: Research
    const researchFindings = await invokeSubAgent("research", userQuery);

    // Step 2: Synthesis (uses research results)
    const finalReport = await invokeSubAgent(
      "synthesis",
      "Create a cohesive summary of quantum computing developments",
      researchFindings
    );

    console.log("\n=== FINAL REPORT ===\n");
    console.log(finalReport);
  } catch (error) {
    console.error("Error:", error.message);
  }
}

main();
```

### Example 2: Parallel Sub-Agent Execution

```javascript
async function parallelResearchPipeline(userQuery) {
  console.log("🚀 Starting parallel sub-agent invocation\n");

  // Launch multiple sub-agents in parallel
  const [technicalAnalysis, businessAnalysis, trendAnalysis] =
    await Promise.allSettled([
      invokeSubAgent(
        "technical-analyst",
        `Analyze technical aspects of: ${userQuery}`
      ),
      invokeSubAgent(
        "business-analyst",
        `Analyze business implications of: ${userQuery}`
      ),
      invokeSubAgent(
        "trend-analyst",
        `Analyze market trends related to: ${userQuery}`
      ),
    ]);

  // Handle results with error checking
  const results = {};

  if (technicalAnalysis.status === "fulfilled") {
    results.technical = technicalAnalysis.value;
  } else {
    results.technical = `ERROR: ${technicalAnalysis.reason.message}`;
  }

  if (businessAnalysis.status === "fulfilled") {
    results.business = businessAnalysis.value;
  } else {
    results.business = `ERROR: ${businessAnalysis.reason.message}`;
  }

  if (trendAnalysis.status === "fulfilled") {
    results.trends = trendAnalysis.value;
  } else {
    results.trends = `ERROR: ${trendAnalysis.reason.message}`;
  }

  // Synthesis phase
  const synthesis = await invokeSubAgent(
    "synthesis",
    "Combine all analyses into a comprehensive report",
    results
  );

  return {
    technical: results.technical,
    business: results.business,
    trends: results.trends,
    synthesis: synthesis,
    totalAgentsRun: 4,
    successRate:
      Object.values(results).filter((r) => !r.includes("ERROR")).length / 3,
  };
}

// Usage
parallelResearchPipeline("Impact of AI on software development")
  .then((report) => console.log(JSON.stringify(report, null, 2)))
  .catch((error) => console.error("Pipeline failed:", error));
```

### Example 3: Conditional Delegation with Context

```javascript
async function intelligentDelegation(userQuery) {
  // Parent agent analyzes the request
  const analysis = await analyzeRequestType(userQuery);

  console.log(`Query type: ${analysis.type}`);
  console.log(`Complexity: ${analysis.complexity}`);
  console.log(`Recommended agents: ${analysis.recommendedAgents.join(", ")}`);

  // Select agents based on analysis
  const selectedAgents = selectAgents(analysis);

  // Execute selected agents with optimized config
  const results = {};

  for (const agentName of selectedAgents) {
    const config = getAgentConfig(agentName, analysis.complexity);

    console.log(`\nInvoking ${agentName}...`);
    console.log(`Config:`, config);

    results[agentName] = await invokeSubAgent(agentName, userQuery, {
      complexity: analysis.complexity,
      context: analysis.context,
      maxIterations: config.maxIterations,
      tools: config.tools,
    });
  }

  return {
    analysis,
    results,
    agentsUsed: selectedAgents,
  };
}

function analyzeRequestType(query) {
  // Mock analysis
  const isComplex =
    query.length > 100 || query.includes("vs") || query.includes("compare");
  const isCodeRelated = query.includes("code") || query.includes("implement");
  const isBusiness = query.includes("business") || query.includes("market");

  return {
    type: isCodeRelated ? "technical" : isBusiness ? "business" : "general",
    complexity: isComplex ? "high" : "medium",
    recommendedAgents: [
      isCodeRelated && "technical-analyst",
      isBusiness && "business-analyst",
      "research-analyst",
      "synthesis-agent",
    ].filter(Boolean),
    context: {
      codeRelated: isCodeRelated,
      businessFocus: isBusiness,
      requiresValidation: isComplex,
    },
  };
}

function selectAgents(analysis) {
  return analysis.recommendedAgents;
}

function getAgentConfig(agentName, complexity) {
  const baseConfig = {
    maxIterations: complexity === "high" ? 8 : 5,
    maxTokens: complexity === "high" ? 3000 : 2000,
  };

  const agentSpecific = {
    "technical-analyst": {
      tools: ["code-executor", "documentation-search", "github-search"],
      ...baseConfig,
    },
    "business-analyst": {
      tools: ["market-data", "financial-calculator", "news-search"],
      ...baseConfig,
    },
    "research-analyst": {
      tools: ["web-search", "academic-search", "validator"],
      ...baseConfig,
    },
  };

  return agentSpecific[agentName] || baseConfig;
}
```

---

## Best Practices

### 1. **Clear Agent Contracts**

Define explicit input/output contracts for each sub-agent:

```javascript
const AgentContract = {
  research: {
    input: {
      topic: "string",
      sourceCount: "number (min 3, max 10)",
      validationLevel: "string (low|medium|high)",
    },
    output: {
      findings: "array<{source, claim, confidence}>",
      summary: "string",
      contradictions: "array<string>",
    },
    errorCodes: ["NO_SOURCES", "VALIDATION_FAILED", "TIMEOUT"],
  },

  synthesis: {
    input: {
      findings: "array (from research)",
      style: "string (academic|business|technical)",
      maxLength: "number",
    },
    output: {
      text: "string",
      wordCount: "number",
      sources: "array<url>",
    },
    errorCodes: ["INVALID_INPUT", "GENERATION_FAILED"],
  },
};
```

### 2. **Resource Management**

Control resource usage per sub-agent:

```javascript
class ResourceLimiter {
  constructor(limits = {}) {
    this.maxTokens = limits.maxTokens || 3000;
    this.maxIterations = limits.maxIterations || 5;
    this.maxDuration = limits.maxDuration || 30000; // 30 seconds
    this.maxConcurrent = limits.maxConcurrent || 3;
  }

  async executeWithLimits(subAgentFn) {
    const startTime = Date.now();
    let tokenCount = 0;
    let iterationCount = 0;

    const wrappedFn = async () => {
      while (
        Date.now() - startTime < this.maxDuration &&
        iterationCount < this.maxIterations
      ) {
        iterationCount++;

        const result = await subAgentFn({
          iterationCount,
          tokensRemaining: this.maxTokens - tokenCount,
          timeRemaining: this.maxDuration - (Date.now() - startTime),
        });

        tokenCount += result.tokensUsed || 0;

        if (tokenCount > this.maxTokens) {
          throw new Error("Token limit exceeded");
        }

        return result;
      }

      if (iterationCount >= this.maxIterations) {
        throw new Error("Iteration limit exceeded");
      }

      throw new Error("Duration limit exceeded");
    };

    return wrappedFn();
  }
}
```

### 3. **Error Boundaries**

Isolate failures to prevent cascading errors:

```javascript
async function robustSubAgentInvocation(agentName, task, context) {
  try {
    return await invokeSubAgent(agentName, task, context);
  } catch (error) {
    // Log error
    console.error(`[${agentName}] Error:`, error);

    // Determine severity
    const severity = classifyError(error);

    if (severity === "FATAL") {
      // This agent is critical, propagate error
      throw new AgentInvocationError(`${agentName} failed critically`, {
        cause: error,
        agent: agentName,
      });
    }

    if (severity === "WARNING") {
      // This agent is optional, return partial result
      return {
        success: false,
        partial: true,
        error: error.message,
        recoveryHint: getRecoveryHint(agentName, error),
      };
    }

    // Try alternative agent or tool
    return await invokeAlternative(agentName, task, context);
  }
}

function classifyError(error) {
  if (error.message.includes("Token limit")) return "WARNING";
  if (error.message.includes("Timeout")) return "WARNING";
  if (error.message.includes("No sources found")) return "WARNING";
  if (error.message.includes("API error")) return "FATAL";
  return "UNKNOWN";
}

class AgentInvocationError extends Error {
  constructor(message, context) {
    super(message);
    this.name = "AgentInvocationError";
    this.context = context;
  }
}
```

### 4. **Monitoring & Observability**

Track sub-agent performance:

```javascript
class SubAgentMonitor {
  constructor() {
    this.metrics = {};
  }

  recordInvocation(agentName, result) {
    if (!this.metrics[agentName]) {
      this.metrics[agentName] = {
        invocations: 0,
        successful: 0,
        failed: 0,
        avgDuration: 0,
        totalTokens: 0,
        errors: [],
      };
    }

    const metric = this.metrics[agentName];
    metric.invocations++;

    if (result.success) {
      metric.successful++;
      metric.avgDuration =
        (metric.avgDuration * (metric.successful - 1) + result.duration) /
        metric.successful;
      metric.totalTokens += result.tokensUsed || 0;
    } else {
      metric.failed++;
      metric.errors.push({
        timestamp: Date.now(),
        error: result.error,
        context: result.context,
      });
    }
  }

  getReport() {
    const report = {};

    for (const [agent, metric] of Object.entries(this.metrics)) {
      report[agent] = {
        ...metric,
        successRate:
          ((metric.successful / metric.invocations) * 100).toFixed(2) + "%",
        avgTokensPerInvocation: Math.round(
          metric.totalTokens / metric.invocations
        ),
        lastErrors: metric.errors.slice(-3),
      };
    }

    return report;
  }
}

// Usage
const monitor = new SubAgentMonitor();

async function monitoredInvocation(agentName, task) {
  const startTime = Date.now();

  try {
    const result = await invokeSubAgent(agentName, task);

    monitor.recordInvocation(agentName, {
      success: true,
      duration: Date.now() - startTime,
      tokensUsed: result.metrics?.tokensUsed,
    });

    return result;
  } catch (error) {
    monitor.recordInvocation(agentName, {
      success: false,
      duration: Date.now() - startTime,
      error: error.message,
    });

    throw error;
  }
}

// Periodic reporting
setInterval(() => {
  console.log("Sub-Agent Performance Report:");
  console.log(JSON.stringify(monitor.getReport(), null, 2));
}, 60000); // Every minute
```

### 5. **Caching & Memoization**

Avoid redundant sub-agent invocations:

```javascript
class SubAgentCache {
  constructor(ttl = 3600000) {
    // 1 hour default
    this.cache = new Map();
    this.ttl = ttl;
  }

  getKey(agentName, task, context) {
    // Create deterministic cache key
    return `${agentName}:${JSON.stringify({ task, context })}`;
  }

  async getOrInvoke(agentName, task, context) {
    const key = this.getKey(agentName, task, context);

    // Check cache
    if (this.cache.has(key)) {
      const cached = this.cache.get(key);
      if (Date.now() - cached.timestamp < this.ttl) {
        console.log(`[CACHE HIT] ${agentName}`);
        return cached.result;
      } else {
        this.cache.delete(key);
      }
    }

    // Not in cache, invoke
    console.log(`[CACHE MISS] ${agentName} - invoking...`);
    const result = await invokeSubAgent(agentName, task, context);

    // Store in cache
    this.cache.set(key, {
      result,
      timestamp: Date.now(),
    });

    return result;
  }

  clearExpired() {
    const now = Date.now();
    for (const [key, cached] of this.cache.entries()) {
      if (now - cached.timestamp > this.ttl) {
        this.cache.delete(key);
      }
    }
  }
}

// Usage
const cache = new SubAgentCache();

async function cachedSubAgentInvocation(agentName, task, context) {
  return cache.getOrInvoke(agentName, task, context);
}

// Clear expired every 10 minutes
setInterval(() => cache.clearExpired(), 600000);
```

---

## Advanced Patterns

### 1. **Agent Feedback Loops**

Sub-agents can provide feedback to parent:

```javascript
async function feedbackLoopPattern(task) {
  let refinement = 0;
  let previousResult = null;

  while (refinement < 3) {
    // Max 3 refinement cycles
    const result = await invokeSubAgent("analysis", task, {
      previousFeedback: previousResult?.feedback,
      refinementCycle: refinement,
    });

    console.log(`Refinement ${refinement}:`, result.confidence);

    if (result.confidence > 0.9) {
      // Good enough
      return result;
    }

    // Ask parent for guidance
    const parentFeedback = await askParentAgent(
      "What should we focus on next?",
      { currentResult: result }
    );

    previousResult = { ...result, feedback: parentFeedback };
    refinement++;
  }

  return previousResult;
}
```

### 2. **Multi-Objective Optimization**

Sub-agents optimize for different objectives:

```javascript
async function multiObjectiveOptimization(requirements) {
  const objectives = [
    { name: "performance", weight: 0.4 },
    { name: "cost", weight: 0.3 },
    { name: "usability", weight: 0.3 },
  ];

  const results = await Promise.all(
    objectives.map((obj) =>
      invokeSubAgent(`${obj.name}-optimizer`, `Optimize for: ${obj.name}`, {
        weight: obj.weight,
      })
    )
  );

  // Combine results using weighted scoring
  const combined = combineResults(results, objectives);
  return combined;
}

function combineResults(results, objectives) {
  // Each result has scores for all objectives
  // Combine using weighted average
  const scores = {
    performance: 0,
    cost: 0,
    usability: 0,
  };

  for (const obj of objectives) {
    for (const result of results) {
      if (result.agent === `${obj.name}-optimizer`) {
        scores[obj.name] += result.score * obj.weight;
      }
    }
  }

  return {
    scores,
    recommendation: selectBestOption(scores),
  };
}
```

### 3. **Dynamic Agent Selection**

Automatically choose agents based on task:

```javascript
async function dynamicAgentSelection(task, availableAgents) {
  // Analyze task
  const taskCharacteristics = analyzeTask(task);

  // Score agents
  const agentScores = availableAgents.map((agent) => ({
    agent: agent.name,
    score: scoreAgent(agent, taskCharacteristics),
  }));

  // Sort by score
  agentScores.sort((a, b) => b.score - a.score);

  // Try agents in order until one succeeds
  for (const { agent } of agentScores) {
    try {
      const result = await invokeSubAgent(agent, task);
      return { result, agentUsed: agent };
    } catch (error) {
      console.log(`Agent ${agent} failed, trying next...`);
    }
  }

  throw new Error("All agents failed");
}

function scoreAgent(agent, taskCharacteristics) {
  let score = 0;

  if (agent.specializations.includes(taskCharacteristics.domain)) {
    score += 50;
  }

  if (agent.languages.includes(taskCharacteristics.language)) {
    score += 30;
  }

  score += agent.recentSuccessRate * 20;

  return score;
}
```

---

## Troubleshooting & Monitoring

### Common Issues & Solutions

#### Issue 1: Sub-Agent Stuck in Loop

**Symptom:** Iteration count keeps increasing, never reaches `end_turn`

**Causes:**

- Sub-agent keeps requesting tools
- Tools return same result repeatedly
- Goal is unrealistic

**Solutions:**

```javascript
// Add quality checks
if (iteration > 3 && tokensUsed > maxTokens * 0.8) {
  // Force completion even if not fully satisfied
  return forceCompletion(messages);
}

// Detect loops
if (lastFiveResults.every((r) => r === currentResult)) {
  // Break out of loop
  return { partial: true, result: currentResult };
}
```

#### Issue 2: High Token Usage

**Symptom:** Sub-agents consuming 3000+ tokens per invocation

**Causes:**

- Tools returning verbose output
- Many iterations
- Large context

**Solutions:**

```javascript
// Summarize tool outputs
const summarized = await summarizeTool Result(toolOutput, "50 words");
messages.push({ role: "user", content: summarized });

// Progressive refinement
if (iteration > 2) {
  // Ask for shorter answers
  messages.push({
    role: "user",
    content: "Provide summary in 1-2 sentences"
  });
}
```

#### Issue 3: Parent-Sub-Agent Mismatch

**Symptom:** Sub-agent returns format different than expected

**Causes:**

- Unclear contract
- Sub-agent misunderstood task
- Context not passed correctly

**Solutions:**

```javascript
// Validate output
const schema = JSON.parse(AgentContract[agentName].output);
const validated = validateAgainstSchema(result, schema);

if (!validated.valid) {
  // Ask sub-agent to reformat
  return await invokeSubAgent(
    agentName,
    `Please restructure your response to: ${JSON.stringify(schema)}`
  );
}
```

### Monitoring Checklist

```
□ Token usage per sub-agent
□ Iteration counts
□ Success/failure rates
□ Average latency
□ Error types and frequencies
□ Cache hit rates (if using caching)
□ Cost per sub-agent
□ Quality of outputs (using validation)
□ User satisfaction with results
□ Sub-agent combinations that work well
```

---

## Real-World Use Cases

### Use Case 1: Software Development Assistant

```
User Request: "Build a REST API for managing users"
                    ↓
        Main Agent (orchestrator)
        ├─ Requirements Sub-Agent
        │   └─ Clarifies requirements, generates spec
        ├─ Design Sub-Agent
        │   └─ Creates architecture, DB schema
        ├─ Implementation Sub-Agent
        │   └─ Writes code with tests
        └─ Review Sub-Agent
            └─ Checks quality, security, best practices
```

### Use Case 2: Market Research Pipeline

```
User Request: "Analyze SaaS market for AI tools"
                    ↓
        Main Agent
        ├─ Research Sub-Agent (parallel)
        │   └─ Finds market data, company info
        ├─ Analysis Sub-Agent (parallel)
        │   └─ Analyzes trends, competitors
        └─ Synthesis Sub-Agent
            └─ Creates comprehensive report
```

### Use Case 3: Content Generation Pipeline

```
User Request: "Write blog post about Kubernetes"
                    ↓
        Main Agent
        ├─ Research Sub-Agent
        │   └─ Gathers latest info
        ├─ Outline Sub-Agent
        │   └─ Creates structure
        ├─ Content Sub-Agent (parallel sections)
        │   ├─ Introduction
        │   ├─ Main concepts
        │   └─ Practical examples
        ├─ Review Sub-Agent
        │   └─ Checks quality
        └─ Optimization Sub-Agent
            └─ SEO optimization
```

---

## Summary & Key Takeaways

**Core Principles:**

1. **Sub-agents are autonomous loops** — They run full agentic loops, not just tools
2. **Clear contracts** — Define input/output expectations precisely
3. **Resource limits** — Always set max iterations, tokens, duration
4. **Error boundaries** — Isolate failures to prevent cascading
5. **Monitoring** — Track every invocation for insights
6. **Caching** — Avoid redundant expensive invocations
7. **Context passing** — Preserve and share relevant context

**Pattern Selection Guide:**

| Pattern      | Best For              | Trade-offs             |
| ------------ | --------------------- | ---------------------- |
| Sequential   | Dependent tasks       | Slower, simpler        |
| Parallel     | Independent tasks     | Faster, complex        |
| Conditional  | Variable requirements | More logic, flexible   |
| Hierarchical | Complex problems      | Most powerful, hardest |
| Hybrid       | Mixed workloads       | Balanced approach      |

**Next Steps:**

1. Start with sequential delegation (simplest)
2. Profile token usage and latency
3. Introduce parallel execution where beneficial
4. Add caching for repeated patterns
5. Implement monitoring and alerts
6. Gradually increase complexity

---

## Additional Resources

- [Anthropic Agentic Loop Documentation](#)
- [Tool Use Best Practices](#)
- [Cost Optimization Guide](#)
- [Scaling Agents in Production](#)
- [Agent Testing Strategies](#)

---

**Last Updated:** 2026  
**Version:** 1.0
