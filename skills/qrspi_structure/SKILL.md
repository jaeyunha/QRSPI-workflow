---
name: qrspi_structure
description: Create structure outline — vertical phases with test checkpoints (QRSPI step 4 of 5)
---

# Structure Outline

You create a concise structure outline that describes HOW we'll implement the approved design. This is step 4 of the QRSPI workflow. Think of this as C header files for the implementation — signatures, types, and phase boundaries, not the full code.

## Task Directory

All QRSPI artifacts for a task live together in one directory:

```
thoughts/shared/tasks/ENG-XXXX-short-description/
├── ENG-XXXX-01-questions.md
├── ENG-XXXX-02-research.md
├── ENG-XXXX-03-design.md
├── ENG-XXXX-04-structure.md    <-- you create this
└── ENG-XXXX-05-plan.md
```

## Inputs

You need:
1. The ticket
2. The research document (`ENG-XXXX-02-research.md` in the task directory)
3. The design document (`ENG-XXXX-03-design.md` in the task directory)

If any are missing, ask. Read all FULLY before proceeding.

## Process

1. Read the ticket, research, and design documents FULLY
2. Identify the natural vertical slices for this feature
3. Present a draft outline to the user — DO NOT finalize without their input
4. Iterate until the user approves the phase ordering and scope
5. Save to `thoughts/shared/tasks/ENG-XXXX-description/ENG-XXXX-04-structure.md`

## Vertical vs Horizontal Plans

WRONG (horizontal):
- Phase 1: all database changes
- Phase 2: all service layer changes
- Phase 3: all API changes
- Phase 4: all UI changes
- Result: 1200 lines of code before anything is testable

RIGHT (vertical):
- Phase 1: feature A end-to-end (DB + service + API + UI for one slice)
- Phase 2: feature B end-to-end
- Phase 3: edge cases + polish
- Result: each phase is independently testable

Each phase should be independently verifiable. If Phase 2 breaks, Phase 1 still works.

## Initial Presentation (MANDATORY)

Before writing the outline, present:

```
Here's how I'd break this into vertical phases:

Phase 1: [Name] — [one sentence, what becomes testable after this]
Phase 2: [Name] — [one sentence]
Phase 3: [Name] — [one sentence]
Phase N: Testing & Polish

Does this ordering make sense? Should any phases be split, merged, or reordered?
```

Wait for approval. Iterate if needed.

## Outline Template (~2 pages max)

```markdown
# Structure Outline: [Feature Name]

**Ticket**: [ref]
**Design**: [path to design doc]
**Date**: [today]

## Phase 1: [Name] — [what this achieves]

**Scope**: [which vertical slice]
**Key changes**:
- `[file/component]`: [what changes — new types, signatures, or brief description]
- `[file/component]`: [what changes]
**Verification**: [how to confirm this phase works]

---

## Phase 2: [Name] — [what this achieves]

**Scope**: [which vertical slice]
**Key changes**:
- `[file/component]`: [what changes]
**Verification**: [how to confirm this phase works]

---

## Phase N: Testing & Polish

**Scope**: edge cases, error handling, cleanup
**Key changes**:
- [tests to add]
- [error handling to add]
**Verification**: `make check-test` passes, manual verification of [scenarios]
```

## When to Add Detail

If you think the implementing agent might get a phase wrong, expand that phase to show specific types and function signatures:

```
**Key changes**:
- `src/notifications/types.ts`: Add `EmailNotification` type: `{ recipient: string, template: string, priority?: number }`
- `src/notifications/channels/email.ts`: New `NotificationChannel<EmailNotification>` implementation
- `src/notifications/NotificationRegistry.ts`: Register `email` in channel map
```

Keep phases you're confident about high-level. Expand only where ambiguity is risky.

## What NOT To Do

- Do NOT write full implementation code — just signatures and types where needed
- Do NOT create horizontal phases (all DB, then all services, then all API)
- Do NOT skip user review — present the outline and iterate
- Do NOT exceed ~2 pages — if it's longer, phases are too detailed
- Do NOT re-debate design decisions — those are resolved in the design doc
