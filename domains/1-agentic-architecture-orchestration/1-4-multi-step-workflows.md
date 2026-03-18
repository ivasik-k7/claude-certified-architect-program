# Multi-Step Workflows: Complete Methodology Guide

**Domain:** 1 - Agentic Architecture & Orchestration  
**Topic:** 1-4 - Multi-Step Workflows  
**Version:** 1.0  
**Audience:** Claude Certified Architect Candidates

---

## Table of Contents

1. [Fundamentals](#fundamentals)
2. [Workflow Architecture](#workflow-architecture)
3. [State Management](#state-management)
4. [Step Execution Models](#step-execution-models)
5. [Error Handling & Recovery](#error-handling--recovery)
6. [Workflow Orchestration](#workflow-orchestration)
7. [Advanced Patterns](#advanced-patterns)
8. [Production Implementation](#production-implementation)
9. [Best Practices & Certification Guide](#best-practices--certification-guide)

---

## Fundamentals

### What is a Multi-Step Workflow?

A **multi-step workflow** is a sequence of interconnected operations (steps) that must be executed in a coordinated manner to achieve a complex goal. Each step:

- Takes input from previous steps or external sources
- Processes using Claude API or external tools
- Produces output for subsequent steps
- May have conditional branching
- Can retry on failure
- Maintains state across execution

**Core Characteristics:**

| Characteristic     | Description                                       |
| ------------------ | ------------------------------------------------- |
| **Sequential**     | Steps have defined order (though can be parallel) |
| **Stateful**       | Maintains context and state between steps         |
| **Fault-tolerant** | Handles failures gracefully with recovery         |
| **Observable**     | Tracks progress and state transitions             |
| **Reversible**     | Can rollback or compensate on failure             |

**Distinction from Agentic Loops:**

```
Agentic Loop:
- Single agent iterates until goal achieved
- Internal reasoning loop (Plan→Tool→Observe→Reflect)
- One conceptual task
- Agent controls iteration count

Multi-Step Workflow:
- Multiple operations execute in sequence/parallel
- External orchestration decides flow
- Complex tasks decomposed into steps
- Orchestrator controls what executes and when
```

### When to Use Multi-Step Workflows

**Use workflows when:**

- Task naturally decomposes into sequential stages
- Different tools/models needed per step
- Intermediate results need validation
- Long-running operations (> 30 seconds)
- Human intervention may be needed
- Strong state consistency required
- Clear success/failure criteria per step

**Don't use workflows if:**

- Single agentic loop solves it better
- No clear step boundaries
- Real-time responsiveness critical
- Minimal state sharing between operations

### Example: Document Processing Workflow

```
Input: PDF document
  ↓
Step 1: Extract text from PDF
  ↓
Step 2: Parse structured data (Claude API)
  ↓
Step 3: Validate extracted data
  ↓
Step 4: Generate summary (Claude API)
  ↓
Step 5: Store results
  ↓
Output: Processed data + summary
```

---

## Workflow Architecture

### Component Model

A multi-step workflow consists of:

```
┌─────────────────────────────────────┐
│      Workflow Definition             │
│  (DAG, state machine, or linear)    │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   Workflow Orchestrator             │
│  (Executes steps, manages state)    │
└──────────────┬──────────────────────┘
               ↓
      ┌────────┴────────┐
      ↓                 ↓
  ┌─────────┐      ┌─────────┐
  │ Step 1  │      │ Step 2  │
  └────┬────┘      └────┬────┘
       ↓                ↓
  ┌──────────────────────────┐
  │   State Store            │
  │  (Context, results)      │
  └──────────────────────────┘
```

### Workflow Definition Patterns

#### Pattern 1: Linear Workflow (DAG)

```
Step A → Step B → Step C → Step D
```

**Structure:** Directed acyclic graph  
**When to use:** Clear sequential dependencies  
**Complexity:** Low

#### Pattern 2: Branching Workflow

```
         ┌→ Step B1 ┐
Step A →┤           ├→ Step D
         └→ Step B2 ┘
              ↓
           Step C
```

**Structure:** Conditional branching  
**When to use:** Different paths based on conditions  
**Complexity:** Medium

#### Pattern 3: Parallel Workflow

```
Step A →┬→ Step B ┐
        ├→ Step C ├→ Step D
        └→ Step E ┘
```

**Structure:** Independent parallel execution  
**When to use:** Steps don't depend on each other  
**Complexity:** Medium

#### Pattern 4: Loop Workflow

```
Step A → Step B → Decision → [Loop back to B or continue]
                      ↓
                    Step C
```

**Structure:** Conditional loops  
**When to use:** Retry logic, iterative refinement  
**Complexity:** Medium

### State Machine Model

States in a workflow:

```
┌─────────────────────────────────────┐
│          INITIAL                    │
└────────────┬────────────────────────┘
             │ Start workflow
             ↓
┌─────────────────────────────────────┐
│      STEP_1_RUNNING                 │
└────────────┬────────────────────────┘
             │ Step 1 complete
             ↓
┌─────────────────────────────────────┐
│      STEP_2_RUNNING                 │
└────────────┬────────────────────────┘
             │ Step 2 complete
             ↓
┌─────────────────────────────────────┐
│      STEP_3_RUNNING                 │
└────────────┬────────────────────────┘
             │ Step 3 complete
             ↓
┌─────────────────────────────────────┐
│          COMPLETED                  │
└─────────────────────────────────────┘
```

---

## State Management

### State Structure

```python
class WorkflowState:
    """Represents workflow execution state"""

    # Metadata
    workflow_id: str                    # Unique workflow execution ID
    current_step: int                   # Current step index
    status: WorkflowStatus              # RUNNING, PAUSED, COMPLETED, FAILED

    # Context (passed between steps)
    context: Dict[str, Any]             # User input, configuration
    intermediate_results: Dict[str, Any] # Results from previous steps

    # Tracking
    step_history: List[StepExecution]   # History of executed steps
    error_history: List[WorkflowError]  # Errors encountered
    created_at: datetime
    updated_at: datetime
```

### State Transitions

**Valid transitions:**

```
INITIAL
  ↓
RUNNING → PAUSED → RUNNING (resume)
  ↓
FAILED ← (retry from step N)
  ↓
COMPLETED
```

**State persistence:**

- State stored in database between steps
- Enables recovery if workflow crashes
- Allows pause/resume functionality
- Enables audit trail

### Context Passing

**Context flows through workflow:**

```python
# Step 1: Initialize context
context = {
    "user_input": "Process this document",
    "user_id": "user_123",
    "preferences": {...}
}

# Step 2: Step A executes and adds results
result_a = step_a(context)
context["step_a_result"] = result_a
context["step_a_timestamp"] = datetime.now()

# Step 3: Step B uses Step A's result
result_b = step_b(context)
context["step_b_result"] = result_b

# Step 4: Step C can access both
result_c = step_c(context)
```

**Best Practices:**

- Immutable context per step (copy on write)
- Version intermediate results
- Clear naming (avoid overwriting)
- Log context changes
- Limit context size (especially for Claude API)

---

## Step Execution Models

### Model 1: Tool-Based Steps

**Definition:** Step invokes external tool/API (not Claude)

```python
async def step_extract_pdf(context: Dict) -> Dict:
    """Extract text from PDF using pdf library"""
    pdf_path = context["input_file"]

    try:
        with open(pdf_path, 'rb') as f:
            pdf_reader = PdfReader(f)
            text = ""
            for page in pdf_reader.pages:
                text += page.extract_text()

        return {
            "status": "success",
            "extracted_text": text,
            "page_count": len(pdf_reader.pages),
            "duration_ms": elapsed_time
        }
    except Exception as e:
        return {
            "status": "error",
            "error": str(e),
            "error_type": "PDF_EXTRACTION_FAILED"
        }
```

**Characteristics:**

- Fast, deterministic
- Good for data transformation
- No LLM tokens used
- Limited to structured data

### Model 2: Claude API Steps

**Definition:** Step uses Claude for reasoning/generation

```python
async def step_analyze_with_claude(context: Dict) -> Dict:
    """Use Claude to analyze extracted text"""

    client = Anthropic()
    extracted_text = context["step_extract_pdf_result"]["extracted_text"]

    message = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=2000,
        system="You are a document analyzer. Extract key information.",
        messages=[
            {
                "role": "user",
                "content": f"Analyze this document:\n\n{extracted_text}"
            }
        ]
    )

    analysis = message.content[0].text

    return {
        "status": "success",
        "analysis": analysis,
        "model": message.model,
        "tokens_used": message.usage.output_tokens + message.usage.input_tokens
    }
```

**Characteristics:**

- Intelligent analysis/generation
- Token-based cost
- Slower than tools
- Handles unstructured data well

### Model 3: Agentic Steps

**Definition:** Step runs a mini agentic loop

```python
async def step_research_with_agent(context: Dict) -> Dict:
    """Run research agent to gather information"""

    query = context["research_query"]

    # Initialize agentic loop state
    messages = [
        {"role": "user", "content": f"Research: {query}"}
    ]

    max_iterations = 5
    iteration = 0

    while iteration < max_iterations:
        # Claude decides next action
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=2000,
            tools=[web_search_tool, fetch_tool],
            messages=messages
        )

        # Append assistant response
        messages.append({"role": "assistant", "content": response.content})

        if response.stop_reason == "end_turn":
            # Agent finished
            return {
                "status": "success",
                "result": extract_text(response.content),
                "iterations": iteration + 1
            }

        if response.stop_reason == "tool_use":
            # Execute tools and continue loop
            tool_results = []
            for block in response.content:
                if block.type == "tool_use":
                    result = await execute_tool(block.name, block.input)
                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": result
                    })

            messages.append({"role": "user", "content": tool_results})
            iteration += 1

    return {
        "status": "error",
        "error": "Max iterations reached",
        "partial_result": extract_text(messages[-2]["content"])
    }
```

**Characteristics:**

- Full reasoning capability
- Iterative problem-solving
- High token usage
- Unpredictable duration

---

## Error Handling & Recovery

### Error Classification

```python
class ErrorSeverity(Enum):
    RECOVERABLE = 1      # Retry with backoff
    RETRYABLE = 2        # Retry from current step
    DEGRADED = 3         # Continue with fallback
    FATAL = 4             # Stop workflow

class WorkflowError:
    step_id: str
    error_type: str      # "TIMEOUT", "API_ERROR", etc.
    severity: ErrorSeverity
    message: str
    timestamp: datetime
    retry_count: int
```

### Recovery Strategy by Severity

```python
async def execute_step_with_recovery(
    step_id: str,
    step_fn,
    context: Dict,
    max_retries: int = 3
) -> Dict:
    """Execute step with automatic recovery"""

    retry_count = 0
    last_error = None

    while retry_count < max_retries:
        try:
            # Execute step with timeout
            result = await asyncio.wait_for(
                step_fn(context),
                timeout=300  # 5 minutes
            )

            if result.get("status") == "success":
                return result

            # Non-fatal error
            error_type = result.get("error_type")

            if error_type in ["RATE_LIMIT", "TEMPORARY_ERROR"]:
                # Recoverable: exponential backoff
                wait_time = 2 ** retry_count
                await asyncio.sleep(wait_time)
                retry_count += 1
                continue

            if error_type == "VALIDATION_FAILED":
                # Degraded: return partial result
                return {
                    "status": "degraded",
                    "result": result.get("partial_result"),
                    "error": result.get("error")
                }

            # Fatal error
            return {
                "status": "fatal",
                "error": result.get("error"),
                "error_type": error_type
            }

        except asyncio.TimeoutError:
            # Timeout: retry with longer timeout
            last_error = WorkflowError(
                step_id=step_id,
                error_type="TIMEOUT",
                severity=ErrorSeverity.RETRYABLE,
                message="Step execution timed out"
            )
            retry_count += 1
            continue

        except Exception as e:
            # Unexpected error
            last_error = WorkflowError(
                step_id=step_id,
                error_type="UNEXPECTED",
                severity=ErrorSeverity.FATAL,
                message=str(e)
            )
            break

    # All retries exhausted
    return {
        "status": "failed",
        "error": str(last_error),
        "retry_count": retry_count
    }
```

### Compensation Actions

When workflow fails, optionally run compensation (rollback):

```python
async def workflow_with_compensation(steps: List, context: Dict):
    """Execute workflow with compensation on failure"""

    executed_steps = []

    try:
        for step in steps:
            result = await execute_step(step, context)

            if result["status"] != "success":
                raise WorkflowStepFailed(step.id, result)

            executed_steps.append((step, result))
            context[f"step_{step.id}_result"] = result

        return {"status": "success", "result": context}

    except WorkflowStepFailed as e:
        # Compensation: reverse executed steps
        for step, result in reversed(executed_steps):
            if hasattr(step, 'compensate'):
                try:
                    await step.compensate(context)
                except Exception as comp_error:
                    logger.error(f"Compensation failed: {comp_error}")

        return {
            "status": "failed",
            "failed_step": e.step_id,
            "error": str(e)
        }
```

---

## Workflow Orchestration

### Orchestrator Pattern

```python
class WorkflowOrchestrator:
    """Manages multi-step workflow execution"""

    def __init__(self, workflow_def: WorkflowDefinition, state_store):
        self.workflow_def = workflow_def
        self.state_store = state_store  # Database, Redis, etc.

    async def execute(self, workflow_input: Dict) -> Dict:
        """Execute complete workflow"""

        # Initialize workflow state
        workflow_id = generate_id()
        state = WorkflowState(
            workflow_id=workflow_id,
            current_step=0,
            status=WorkflowStatus.RUNNING,
            context=workflow_input,
            intermediate_results={},
            step_history=[],
            error_history=[]
        )

        # Save initial state
        await self.state_store.save(workflow_id, state)

        # Execute steps
        try:
            while state.current_step < len(self.workflow_def.steps):
                step_def = self.workflow_def.steps[state.current_step]

                # Execute step with recovery
                step_result = await self.execute_step(
                    step_def,
                    state.context,
                    state.intermediate_results
                )

                # Update state
                state.intermediate_results[f"step_{step_def.id}"] = step_result
                state.step_history.append({
                    "step_id": step_def.id,
                    "status": step_result["status"],
                    "timestamp": datetime.now()
                })

                # Handle step failure
                if step_result["status"] != "success":
                    state.status = WorkflowStatus.FAILED
                    state.error_history.append({
                        "step_id": step_def.id,
                        "error": step_result.get("error")
                    })
                    await self.state_store.save(workflow_id, state)
                    return state

                # Advance to next step
                state.current_step += 1
                await self.state_store.save(workflow_id, state)

            # All steps completed
            state.status = WorkflowStatus.COMPLETED
            await self.state_store.save(workflow_id, state)
            return state

        except Exception as e:
            state.status = WorkflowStatus.FAILED
            state.error_history.append({
                "error": str(e),
                "timestamp": datetime.now()
            })
            await self.state_store.save(workflow_id, state)
            raise

    async def execute_step(self, step_def, context, previous_results):
        """Execute single step with error handling"""

        step_input = {
            "context": context,
            "previous_results": previous_results
        }

        result = await execute_step_with_recovery(
            step_id=step_def.id,
            step_fn=step_def.execute,
            context=step_input
        )

        return result

    async def pause(self, workflow_id: str):
        """Pause workflow at current step"""
        state = await self.state_store.get(workflow_id)
        state.status = WorkflowStatus.PAUSED
        await self.state_store.save(workflow_id, state)

    async def resume(self, workflow_id: str):
        """Resume paused workflow"""
        state = await self.state_store.get(workflow_id)
        state.status = WorkflowStatus.RUNNING
        # Continue from current step
        return await self.execute_from_step(state.current_step, state)
```

---

## Advanced Patterns

### Pattern 1: Conditional Branching

```python
async def step_evaluate_content_type(context: Dict) -> Dict:
    """Determine content type and set branch"""

    text = context["extracted_text"]

    message = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=100,
        messages=[{
            "role": "user",
            "content": f"Classify as: EMAIL, REPORT, INVOICE, OTHER\n\n{text[:500]}"
        }]
    )

    classification = message.content[0].text.strip().upper()

    return {
        "status": "success",
        "classification": classification,
        "next_steps": {
            "EMAIL": ["extract_sender", "extract_recipients", "summarize"],
            "REPORT": ["extract_sections", "extract_metrics", "generate_summary"],
            "INVOICE": ["extract_items", "extract_amounts", "validate_total"],
            "OTHER": ["generic_analysis"]
        }[classification]
    }
```

### Pattern 2: Parallel Step Execution

```python
async def execute_parallel_steps(
    step_definitions: List[StepDef],
    context: Dict
) -> Dict:
    """Execute multiple steps in parallel"""

    tasks = [
        execute_step(step_def, context)
        for step_def in step_definitions
    ]

    results = await asyncio.gather(*tasks, return_exceptions=True)

    # Aggregate results
    aggregated = {}
    for step_def, result in zip(step_definitions, results):
        if isinstance(result, Exception):
            aggregated[step_def.id] = {
                "status": "error",
                "error": str(result)
            }
        else:
            aggregated[step_def.id] = result

    return {
        "status": "success" if all(r["status"] == "success" for r in aggregated.values()) else "partial",
        "results": aggregated
    }
```

### Pattern 3: Adaptive Workflow

Workflow adjusts based on runtime conditions:

```python
async def execute_adaptive_workflow(context: Dict) -> Dict:
    """Workflow that adapts based on intermediate results"""

    # Step 1: Analyze input
    analysis = await step_analyze(context)
    context["analysis"] = analysis

    # Step 2: Decide depth based on analysis
    if analysis["complexity"] == "HIGH":
        # Use more sophisticated analysis
        detailed_result = await step_detailed_analysis(context)
        context["detailed_result"] = detailed_result
    else:
        # Use simpler, faster analysis
        simple_result = await step_simple_analysis(context)
        context["simple_result"] = simple_result

    # Step 3: Format results
    final_result = await step_format_results(context)

    return final_result
```

---

## Production Implementation

### Complete Example: Document Processing Workflow

```python
from dataclasses import dataclass
from enum import Enum
import json
from datetime import datetime
import asyncio
from typing import Dict, Any, List, Optional

# ============================================================================
# Data Models
# ============================================================================

class StepStatus(Enum):
    PENDING = "pending"
    RUNNING = "running"
    SUCCESS = "success"
    FAILED = "failed"
    DEGRADED = "degraded"

@dataclass
class StepResult:
    step_id: str
    status: StepStatus
    output: Dict[str, Any]
    error: Optional[str] = None
    duration_ms: int = 0
    tokens_used: int = 0

@dataclass
class WorkflowState:
    workflow_id: str
    steps_executed: List[StepResult]
    current_context: Dict[str, Any]
    overall_status: StepStatus
    created_at: datetime
    updated_at: datetime

# ============================================================================
# Step Definitions
# ============================================================================

class DocumentProcessingStep:
    """Base class for workflow steps"""

    def __init__(self, step_id: str):
        self.step_id = step_id

    async def execute(self, context: Dict) -> StepResult:
        raise NotImplementedError

class ExtractTextStep(DocumentProcessingStep):
    """Extract text from uploaded document"""

    async def execute(self, context: Dict) -> StepResult:
        start_time = datetime.now()

        try:
            file_path = context.get("input_file")

            if not file_path:
                return StepResult(
                    step_id=self.step_id,
                    status=StepStatus.FAILED,
                    output={},
                    error="No input file provided"
                )

            # Simulate PDF text extraction
            # In production, use PyPDF2, pdfplumber, etc.
            extracted_text = f"Extracted text from {file_path}"

            duration = (datetime.now() - start_time).total_seconds() * 1000

            return StepResult(
                step_id=self.step_id,
                status=StepStatus.SUCCESS,
                output={
                    "extracted_text": extracted_text,
                    "file_size_bytes": 5000,
                    "page_count": 3
                },
                duration_ms=int(duration)
            )

        except Exception as e:
            return StepResult(
                step_id=self.step_id,
                status=StepStatus.FAILED,
                output={},
                error=str(e)
            )

class AnalyzeWithClaudeStep(DocumentProcessingStep):
    """Use Claude to analyze extracted text"""

    def __init__(self, step_id: str, client):
        super().__init__(step_id)
        self.client = client

    async def execute(self, context: Dict) -> StepResult:
        start_time = datetime.now()

        try:
            extracted_text = context.get("step_extract_text", {}).get("extracted_text")

            if not extracted_text:
                return StepResult(
                    step_id=self.step_id,
                    status=StepStatus.FAILED,
                    output={},
                    error="No extracted text available"
                )

            # Call Claude API
            message = self.client.messages.create(
                model="claude-3-5-sonnet-20241022",
                max_tokens=1000,
                messages=[
                    {
                        "role": "user",
                        "content": f"Analyze this document:\n\n{extracted_text}"
                    }
                ]
            )

            analysis = message.content[0].text
            tokens_used = message.usage.output_tokens + message.usage.input_tokens

            duration = (datetime.now() - start_time).total_seconds() * 1000

            return StepResult(
                step_id=self.step_id,
                status=StepStatus.SUCCESS,
                output={
                    "analysis": analysis,
                    "model": message.model
                },
                duration_ms=int(duration),
                tokens_used=tokens_used
            )

        except Exception as e:
            return StepResult(
                step_id=self.step_id,
                status=StepStatus.FAILED,
                output={},
                error=str(e)
            )

class ValidateResultsStep(DocumentProcessingStep):
    """Validate analysis results"""

    async def execute(self, context: Dict) -> StepResult:
        start_time = datetime.now()

        try:
            analysis = context.get("step_analyze", {}).get("analysis")

            if not analysis or len(analysis) < 10:
                return StepResult(
                    step_id=self.step_id,
                    status=StepStatus.DEGRADED,
                    output={
                        "valid": False,
                        "reason": "Analysis too short"
                    },
                    error="Validation failed but continuing"
                )

            duration = (datetime.now() - start_time).total_seconds() * 1000

            return StepResult(
                step_id=self.step_id,
                status=StepStatus.SUCCESS,
                output={
                    "valid": True,
                    "validation_rules_passed": 5,
                    "confidence": 0.95
                },
                duration_ms=int(duration)
            )

        except Exception as e:
            return StepResult(
                step_id=self.step_id,
                status=StepStatus.FAILED,
                output={},
                error=str(e)
            )

# ============================================================================
# Workflow Orchestrator
# ============================================================================

class DocumentProcessingWorkflow:
    """Orchestrates document processing workflow"""

    def __init__(self, client):
        self.client = client
        self.steps = [
            ExtractTextStep("extract_text"),
            AnalyzeWithClaudeStep("analyze", client),
            ValidateResultsStep("validate")
        ]

    async def execute(self, input_file: str) -> WorkflowState:
        """Execute complete workflow"""

        workflow_id = f"workflow_{datetime.now().timestamp()}"
        state = WorkflowState(
            workflow_id=workflow_id,
            steps_executed=[],
            current_context={
                "input_file": input_file
            },
            overall_status=StepStatus.RUNNING,
            created_at=datetime.now(),
            updated_at=datetime.now()
        )

        print(f"Starting workflow {workflow_id}")

        try:
            for step in self.steps:
                print(f"Executing step: {step.step_id}")

                # Execute step
                result = await step.execute(state.current_context)
                state.steps_executed.append(result)

                # Update context with step result
                state.current_context[f"step_{step.step_id}"] = result.output

                # Handle failure
                if result.status == StepStatus.FAILED:
                    print(f"Step {step.step_id} failed: {result.error}")
                    state.overall_status = StepStatus.FAILED
                    break

                if result.status == StepStatus.DEGRADED:
                    print(f"Step {step.step_id} degraded: {result.output}")
                    # Continue with warning

                print(f"Step {step.step_id} completed ({result.duration_ms}ms)")

            # Set final status
            if state.overall_status == StepStatus.RUNNING:
                state.overall_status = StepStatus.SUCCESS

        except Exception as e:
            print(f"Workflow error: {e}")
            state.overall_status = StepStatus.FAILED

        state.updated_at = datetime.now()
        return state

    def get_summary(self, state: WorkflowState) -> Dict:
        """Generate workflow execution summary"""

        total_duration = sum(step.duration_ms for step in state.steps_executed)
        total_tokens = sum(step.tokens_used for step in state.steps_executed)

        return {
            "workflow_id": state.workflow_id,
            "status": state.overall_status.value,
            "steps_completed": len(state.steps_executed),
            "total_duration_ms": total_duration,
            "total_tokens_used": total_tokens,
            "steps": [
                {
                    "id": step.step_id,
                    "status": step.status.value,
                    "duration_ms": step.duration_ms
                }
                for step in state.steps_executed
            ]
        }

# ============================================================================
# Usage Example
# ============================================================================

async def main():
    from anthropic import Anthropic

    client = Anthropic()

    # Create and execute workflow
    workflow = DocumentProcessingWorkflow(client)
    state = await workflow.execute(input_file="sample_document.pdf")

    # Print summary
    summary = workflow.get_summary(state)
    print("\nWorkflow Summary:")
    print(json.dumps(summary, indent=2))

    # Access final context
    if state.overall_status == StepStatus.SUCCESS:
        analysis = state.current_context.get("step_analyze", {}).get("analysis")
        print(f"\nDocument Analysis:\n{analysis}")

if __name__ == "__main__":
    asyncio.run(main())
```

---

## Best Practices & Certification Guide

### Best Practices

1. **State Management**

   - Persist state after each step
   - Enable recovery from crashes
   - Keep context size reasonable (< 100KB per workflow)
   - Version intermediate results

2. **Error Handling**

   - Classify errors by severity
   - Implement exponential backoff for retries
   - Log all errors with context
   - Provide meaningful error messages

3. **Performance**

   - Parallelize independent steps
   - Use caching for expensive operations
   - Monitor token usage (Claude API cost)
   - Set timeouts for all external operations

4. **Observability**

   - Log each step execution
   - Track metrics (duration, tokens, errors)
   - Maintain audit trail
   - Enable debugging from stored state

5. **Testing**
   - Unit test individual steps
   - Integration test complete workflow
   - Test failure scenarios
   - Load test with realistic data

### Certification Topics to Master

**Understand:**

- [ ] Multi-step workflow architecture and components
- [ ] State management across steps
- [ ] Different step execution models (tool, Claude, agentic)
- [ ] Error handling and recovery strategies
- [ ] Orchestration patterns and responsibilities
- [ ] When to use workflows vs agentic loops
- [ ] Advanced patterns (branching, parallel, adaptive)

**Be able to:**

- [ ] Design a workflow for a complex task
- [ ] Implement error recovery
- [ ] Manage state across steps
- [ ] Choose appropriate step types
- [ ] Write production-grade workflow code
- [ ] Debug workflow failures
- [ ] Optimize for cost and performance

**Exam Question Patterns:**

- "Design a workflow for X, explaining each step"
- "How would you handle Y error in a workflow?"
- "When should you use a workflow vs an agentic loop?"
- "Implement state management for X scenario"
- "Optimize this workflow for latency/cost"

---

## Summary

**Multi-step workflows** are essential for complex AI applications. They:

- **Decompose** large tasks into manageable steps
- **Persist** state for reliability
- **Handle** errors gracefully with recovery
- **Orchestrate** different types of operations
- **Provide** observability and debugging
- **Enable** pause/resume and auditing

**Key architectural decisions:**

1. Choose sequential vs parallel execution
2. Decide step types (tool, Claude, agentic)
3. Implement appropriate error recovery
4. Design state structure
5. Plan observability

**Production readiness requires:**

- Robust state persistence
- Comprehensive error handling
- Monitoring and logging
- Testing at all levels
- Performance optimization

---

**Last Updated:** 2026
**Status:** Verified for Claude Certified Architect - Domain 1  
**Python Version:** 3.10+  
**Claude Model:** claude-3-5-sonnet-20241022
