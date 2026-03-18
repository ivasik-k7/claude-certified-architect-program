# Automation Commands

## Content Generation

/generate-summaries

- Creates summaries of all markdown files
- Action: Generates summary.md files in each domain

/build-cheatsheet

- Compiles all key concepts into cheatsheet
- Action: Updates shared/cheatsheet.md with latest content

/create-diagrams-from-code

- Generates diagrams from code examples
- Action: Creates SVG files from Mermaid/PlantUML in code blocks

## Quality Assurance

/check-consistency

- Ensures terminology consistency across files
- Action: Scans for inconsistent term usage, suggests fixes

/validate-links

- Checks all internal links are valid
- Action: Reports broken links and suggests fixes

/format-all

- Standardizes formatting across markdown files
- Action: Applies consistent heading structure, spacing, lists

## Progress Automation

/sync-progress

- Updates progress based on file changes
- Action: Detects new/modified files, updates PROGRESS.md

/next-milestone

- Shows next certification milestone
- Action: Calculates progress toward completion, suggests next steps

/track-time

- Logs current study/work session
- Action: Starts/stops timer, logs to progress tracking
