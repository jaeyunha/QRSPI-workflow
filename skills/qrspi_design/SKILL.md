---
name: qrspi_design
description: Interactive design + structure/spec discussion — align on what we're building (QRSPI step 3 of 5)
---

# Design Discussion

You facilitate an interactive design discussion to build a shared understanding of what we're building. This is step 3 of the QRSPI workflow and produces the highest-leverage artifact: a ~200 line design document that captures every decision before a single line of code is written.

## Why This Matters

This is your chance to "brain dump" everything you think, everything you found, and everything you're unsure about — so the user can do surgery on your understanding before you go write 2,000 lines of code. Do not outsource the thinking. Force every decision to be explicit.

## Task Directory

All QRSPI artifacts for a task live together in one directory:

```
thoughts/shared/tasks/ENG-XXXX-short-description/
├── ENG-XXXX-01-questions.md
├── ENG-XXXX-02-research.md
├── ENG-XXXX-03-design.md    <-- you create this
├── ENG-XXXX-04-structure.md
└── ENG-XXXX-05-plan.md
```

## Inputs

You need two things:
1. The ticket (path or description)
2. The research document (`ENG-XXXX-02-research.md` in the task directory)

If either is missing, ask for it. Read both FULLY before proceeding.

## Process

1. Read the ticket and research document FULLY
2. Analyze the research for relevant patterns, constraints, and existing approaches
3. Present your initial understanding with open questions — DO NOT write the design doc yet
4. Iterate with the user: ask questions, present options, resolve decisions one by one
5. Only after ALL questions are resolved, write the design document
6. Save to `thoughts/shared/tasks/ENG-XXXX-description/ENG-XXXX-03-design.md`

## Step 3: Initial Presentation (MANDATORY — do this BEFORE writing anything)

```
Based on the ticket and research, here's my understanding:

**Current State**: [what exists today, from research, with file:line refs]

**Desired End State**: [what the system looks like after we're done]

**Patterns I found** (confirm these are the RIGHT ones to follow):
- [Pattern A] — `file:line` — [brief description]
- [Pattern B] — `file:line` — [brief description]

**Patterns to AVOID** (ones I found that look wrong or outdated):
- [Anti-pattern] — `file:line` — [why I think we should avoid this]

**Design Questions** (need your input before I can proceed):

1. [Question about approach/tradeoff]
   - A: [description + pros/cons]
   - B: [description + pros/cons]
   - Recommended: [your pick + why]

2. [Another question]
   - A: ...
   - B: ...
```

Wait for user responses. Ask follow-ups. Do NOT proceed until every question is answered and you have confirmed understanding.

## Design Document Template

Only write this AFTER all questions are resolved:

```markdown
# Design: [Feature Name]

**Ticket**: [reference]
**Research**: [path to research doc]
**Date**: [today]
**Status**: draft

## Current State
[What exists today — from research, with file:line refs]

## Desired End State
[What the system looks like after implementation]
[How to verify we're done]

## Patterns to Follow
- [Pattern with file:line reference and brief description]

## Patterns to AVOID
- [Anti-pattern with explanation of why to avoid]

## Design Decisions

### 1. [Topic]
**Choice**: [what was decided]
**Reasoning**: [why, referencing user's input]
**Alternatives rejected**: [what was considered and why not]

### 2. [Topic]
...

## Constraints
- [Technical constraint from research]
- [Business constraint from ticket]

## Open Risks
- [Risk that implementation might surface]
```

## What NOT To Do

- Do NOT write the design doc without asking questions first — this is the whole point
- Do NOT make design decisions unilaterally — present options, get user input
- Do NOT include implementation details (phases, specific file changes) — that is /qrspi_structure
- Do NOT produce more than ~200 lines — concise alignment, not exhaustive spec
- Do NOT skip presenting patterns for confirmation — the user needs to catch bad patterns before they propagate
