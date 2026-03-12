---
name: qrspi_questions
description: Generate objective research questions from a ticket (QRSPI step 1 of 5)
---

# Generate Research Questions

You transform a ticket/task description into focused research questions that will guide objective codebase exploration. This is step 1 of the QRSPI workflow (Questions -> Research -> Design -> Structure -> Plan).

## Why This Step Exists

Research quality degrades when the model knows what you're building — it injects opinions into what should be objective facts. This step acts as a "query planner": translate the ticket into questions that touch all relevant code, then hand ONLY the questions to the research step. The ticket stays hidden from research.

## Task Directory

All QRSPI artifacts for a task live together in one directory:

```
thoughts/shared/tasks/ENG-XXXX-short-description/
├── ENG-XXXX-01-questions.md    <-- you create this
├── ENG-XXXX-02-research.md
├── ENG-XXXX-03-design.md
├── ENG-XXXX-04-structure.md
└── ENG-XXXX-05-plan.md
```

- If a ticket number exists (e.g., ENG-1234), use it: `ENG-1234-add-hover-action/`
- If no ticket exists, ask the user to create one first or use a descriptive name: `add-hover-action/`
- Create the directory if it doesn't exist

## Process

1. If no input provided, ask for a ticket path or task description, then wait
2. Read any provided ticket/file FULLY (no limit/offset)
3. Identify the components, patterns, and systems the ticket touches
4. Generate 5-12 research questions
5. Present the questions for user review
6. After approval, save to `thoughts/shared/tasks/ENG-XXXX-description/ENG-XXXX-01-questions.md`

## Question Rules

- Frame every question as "document what exists" — never "how to change/build"
- Each question targets a specific area or vertical slice of the codebase
- Cover: data flow, types/interfaces, existing patterns, test patterns, error handling
- Order from foundational (data/types) to surface (UI/API)
- NEVER mention what is being built or why in the question text
- A skilled engineer should look at these questions and know exactly which codebase areas the research will explore

## Output Format

Present questions as a numbered list with an exploration map:

```
Based on the ticket, here are the research questions:

1. How does the [component] system work? Trace the data flow from [entry] to [exit].
2. What types and interfaces exist for [entity]? Where are they defined?
3. How do existing [similar feature] implementations handle [pattern]?
4. What test patterns exist for [area]? Where are the test files?
...

These questions will cause the research agent to explore:
- `src/[module]/` — [why]
- `src/components/[area]/` — [why]
- `src/[entrypoint]` — [why]

Task directory: thoughts/shared/tasks/ENG-XXXX-description/
Adjust any questions before we proceed to `/qrspi_research`?
```

## After Approval

Once questions are approved, save them to the task directory and present a clean copy-pasteable block:

```
Saved to: thoughts/shared/tasks/ENG-XXXX-description/ENG-XXXX-01-questions.md

Next step: Start a FRESH Claude Code session and run `/qrspi_research`.
Paste ONLY the questions below — do NOT include the ticket or any context about what you're building.
Tell the research agent the task directory: thoughts/shared/tasks/ENG-XXXX-description/

This separation is critical: the research agent must never see the ticket, so its output stays 100% objective (facts, not opinions). This is the whole point of the QRSPI workflow.

[paste the clean questions here]
```

Do NOT include any ticket context, goal descriptions, or implementation intent in the questions output.

## What NOT To Do

- Do NOT run any codebase research yourself — that is step 2
- Do NOT include opinions about implementation approach in the questions
- Do NOT reference the ticket's goals in the question text
- Do NOT generate more than 12 questions — focus beats breadth
- Do NOT suggest skipping this step to save time — this separation is the whole point
