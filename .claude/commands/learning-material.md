# Learning Material Creation Commands

## Content Development

/create-lesson [domain] [topic]

- Creates a new lesson file with proper structure
- Example: /create-lesson 1-agentic-architecture "Advanced Agent Loops"
- Action: Creates numbered file with template, adds to appropriate domain

/create-exercise [domain] [difficulty]

- Generates a new exercise with solution template
- Example: /create-exercise 2-tool-design intermediate
- Action: Creates exercise file with scaffolding, test cases, and solution stub

/create-diagram [concept] [type]

- Creates diagram description for visualization
- Example: /create-diagram "Subagent Lifecycle" sequence
- Action: Generates Mermaid/PlantUML code for the diagram

## Review & Quality

/review-lesson [file]

- Reviews lesson for completeness and accuracy
- Example: /review-lesson domains/1-4-multi-step-workflows.md
- Action: Checks against exam objectives, suggests improvements

/check-objectives [domain]

- Verifies content covers all exam objectives
- Example: /check-objectives 3-claude-code-workflows
- Action: Compares content against official exam guide objectives

/validate-examples [file]

- Tests code examples for correctness
- Example: /validate-examples domains/2-1-tool-interfaces.md
- Action: Extracts and validates all code blocks

## Progress Tracking

/update-roadmap [completed-item]

- Updates ROADMAP.md with progress
- Example: /update-roadmap "Completed Domain 1 exercises"
- Action: Adds entry to ROADMAP.md with timestamp

/log-study-session [duration] [topic]

- Records study session in progress tracking
- Example: /log-study-session 2h "MCP Integration patterns"
- Action: Appends to progress-tracking/PROGRESS.md

/generate-flashcards [domain]

- Creates flashcards from domain content
- Example: /generate-flashcards 4-prompt-engineering
- Action: Extracts key concepts and generates Q&A format

## Exam Preparation

/practice-quiz [domain] [num-questions]

- Generates practice quiz questions
- Example: /practice-quiz 1-agentic-architecture 10
- Action: Creates quiz with multiple choice, scenario-based questions

/simulate-exam [time-limit]

- Runs full exam simulation
- Example: /simulate-exam 90min
- Action: Generates comprehensive exam with timer

/explain-concept [concept]

- Deep explanation of specific concept with examples
- Example: /explain-concept "subagent delegation patterns"
- Action: Provides detailed explanation with diagrams and code

/compare-patterns [pattern1] [pattern2]

- Compares two architectural patterns
- Example: /compare-patterns "hub-and-spoke" "peer-to-peer"
- Action: Creates comparison table with pros/cons, use cases

## Organization

/reorganize-domains

- Suggests improvements to domain structure
- Action: Analyzes current structure, suggests optimizations

/find-gaps

- Identifies missing topics based on exam guide
- Action: Compares content against official guide, lists gaps

/generate-index

- Creates/updates master index of all materials
- Action: Builds searchable index of all lessons, exercises, resources
