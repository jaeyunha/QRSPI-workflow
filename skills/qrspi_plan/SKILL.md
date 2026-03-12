---
name: qrspi_plan
description: Write the tactical implementation plan from all QRSPI artifacts (QRSPI step 5 of 5)
---

# Write Tactical Plan

You write the detailed implementation plan that an executing agent will follow to build the feature. This is step 5 of the QRSPI workflow. The design discussion and structure outline have already aligned you with the user — this plan fills in the tactical details.

## Task Directory

All QRSPI artifacts for a task live together in one directory:

```
thoughts/shared/tasks/ENG-XXXX-short-description/
├── ENG-XXXX-01-questions.md
├── ENG-XXXX-02-research.md
├── ENG-XXXX-03-design.md
├── ENG-XXXX-04-structure.md
└── ENG-XXXX-05-plan.md    <-- you create this
```

## Inputs

You need:
1. The ticket
2. The research document (`ENG-XXXX-02-research.md` in the task directory)
3. The design document (`ENG-XXXX-03-design.md` in the task directory)
4. The structure outline (`ENG-XXXX-04-structure.md` in the task directory)

If any are missing, ask. Read all FULLY before proceeding.

## Process

1. Read all four input documents FULLY
2. For each phase in the structure outline, flesh out specific file changes and code
3. Spawn sub-agents if you need to look up specific current implementations, function signatures, or test patterns
4. Write the plan to `thoughts/shared/tasks/ENG-XXXX-description/ENG-XXXX-05-plan.md`
5. Present a brief summary — tell the user to spot-check, not deep-review (save that for the code)

## Plan Template

```markdown
# [Feature Name] Implementation Plan

## Overview
[1-2 sentence summary]

## Current State Analysis
[Brief — from design doc]

## Desired End State
[Brief — from design doc, with verification criteria]

## What We're NOT Doing
[Scope boundaries from design doc]

## Implementation Approach
[Strategy and key decisions — from design doc]

## Phase 1: [Name from structure outline]

### Overview
[What this phase accomplishes]

### Changes Required:

#### 1. [Component/File]
**File**: `path/to/file.ts`
**Changes**: [specific changes]

` ` `typescript
// Code to add/modify
` ` `

#### 2. [Component/File]
**File**: `path/to/file.ts`
**Changes**: [specific changes]

### Success Criteria:

#### Automated Verification:
- [ ] `make check` passes
- [ ] `make test` passes
- [ ] `make build` succeeds

#### Manual Verification:
- [ ] [specific manual check from design doc]

**Pause for manual verification before proceeding to Phase 2.**

---

## Phase 2: [Name]
[Same structure...]

---

## Testing Strategy
[Unit tests, E2E tests, manual steps]

## References
- Ticket: [path]
- Research: [path]
- Design: [path]
- Structure: [path]
```

## Sub-Agent Usage

Spawn sub-agents only for tactical lookups:
- Current function signatures you need to match
- Existing test patterns to follow
- Import paths and module structure
- Do NOT use sub-agents for design decisions — those are already resolved

## What NOT To Do

- Do NOT re-debate design decisions — they are resolved in the design doc
- Do NOT restructure the phases — they are set in the structure outline
- Do NOT skip the References section — link all upstream artifacts
- Do NOT ask the user to deeply review this plan — tell them to spot-check it and save the deep review for the actual code
- Do NOT write horizontal phases — the structure outline already enforces vertical slices
