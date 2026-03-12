# QRSPI

**QRSPI: A structured AI coding workflow that prevents models from skipping the thinking.**

From RPI (Research, Plan, Implement) to QRSPI (Questions, Research, Spec/Design, Plan, Implement) — inspired by [Dex Horthy's talk](https://x.com/dexhorthy) on why monolithic AI coding prompts fail and how to fix them.

> **Note:** This is not an official HumanLayer project. The HumanLayer team has not open-sourced their QRSPI workflow yet, so I built these skills based on the concepts from Dex's talks. Feedback is welcome — feel free to open [issues](https://github.com/jaeyunha/QRSPI-workflow/issues) or submit a [pull request](https://github.com/jaeyunha/QRSPI-workflow/pulls).

## The Problem

Most AI coding workflows use a single mega-prompt for planning that tries to do everything at once: gather context, research the codebase, present design options, get feedback, outline phases, and write the plan. This hits three fundamental limits:

```
 THE INSTRUCTION BUDGET PROBLEM
 ================================

 Frontier LLMs reliably follow ~150-200 instructions.

 Your mega-prompt:          ~85 instructions
 CLAUDE.md / system prompt: ~40 instructions
 MCP tools & descriptions:  ~30 instructions
 ──────────────────────────────────────────────
 Total:                     ~155 instructions  <-- rolling the dice

 Result: Model skips the interactive design steps ~50% of the time
         and dumps a 1000-line plan without asking you anything.

 The fix: Split into 5 prompts with ~15-20 instructions each.
```

**Three problems with the old RPI approach:**

1. **Instruction budget exhaustion** — 85+ instructions in one prompt causes the model to skip critical interactive steps (design questions, structure review)
2. **Opinion leakage** — When research knows what you're building, it injects implementation opinions into what should be objective facts
3. **Low-leverage review** — A 1000-line plan takes the same effort to review as the code itself, and the code diverges from the plan anyway

## The Solution: QRSPI

Split the monolithic planning into 5 focused commands, each in a fresh context window with <40 instructions.

```
 Before (RPI):
 ┌──────────────┐     ┌───────────────────────────────────────────┐     ┌──────────────┐
 │   Research    │────>│                  Plan                     │────>│  Implement   │
 │  (1 prompt)  │     │  (1 mega-prompt, 85+ instructions)        │     │  (1 prompt)  │
 └──────────────┘     │  - gather context                         │     └──────────────┘
                      │  - research codebase                      │
                      │  - present design options    SKIPPED 50%  │
                      │  - get user feedback         OF THE TIME  │
                      │  - create structure outline                │
                      │  - write full plan                        │
                      └───────────────────────────────────────────┘

 After (QRSPI):
 ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
 │ Questions│──>│ Research  │──>│  Design  │──>│Structure │──>│   Plan   │
 │ ~15 inst │   │ ~15 inst  │   │ ~20 inst │   │ ~18 inst │   │ ~15 inst │
 │          │   │           │   │          │   │          │   │          │
 │ ticket   │   │ questions │   │ ticket + │   │ ticket + │   │ ticket + │
 │   |      │   │ ONLY (no  │   │ research │   │ research │   │ research │
 │   v      │   │  ticket!) │   │          │   │ + design │   │ + design │
 │questions │   │   |       │   │    |     │   │          │   │ + outline│
 │          │   │   v       │   │    v     │   │    |     │   │          │
 │          │   │ facts doc │   │ ~200 line│   │    v     │   │    |     │
 │          │   │ (no       │   │ design   │   │ ~2 page  │   │    v     │
 │          │   │ opinions) │   │ doc      │   │ outline  │   │ tactical │
 │          │   │           │   │(interact)│   │(vertical)│   │ plan     │
 └──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘
  FRESH CTX      FRESH CTX      FRESH CTX      FRESH CTX      FRESH CTX
```

### Why Each Step Exists

```
 ┌─────────────────────────────────────────────────────────────────────────┐
 │                    THE INFORMATION FLOW                                │
 │                                                                        │
 │  TICKET ──┐                                                           │
 │           │                                                            │
 │           ├──> [1] QUESTIONS ──> questions only (ticket hidden)        │
 │           │                          │                                 │
 │           │                          v                                 │
 │           │                    [2] RESEARCH ──> objective facts        │
 │           │                          │          (zero opinions)        │
 │           │                          │                                 │
 │           ├──────────────────────────┼──> [3] DESIGN                  │
 │           │   ticket + research ─────┘      │                         │
 │           │                                 │  "Where are we going?"  │
 │           │                                 │   - current state       │
 │           │                                 │   - desired end state   │
 │           │                                 │   - patterns to follow  │
 │           │                                 │   - design decisions    │
 │           │                                 v   (interactive Q&A)     │
 │           │                                 │                         │
 │           ├─────────────────────────────────┼──> [4] STRUCTURE        │
 │           │   ticket + research + design ───┘      │                  │
 │           │                                        │  "How do we get  │
 │           │                                        │   there?"        │
 │           │                                        │   - vertical     │
 │           │                                        │     phases       │
 │           │                                        │   - test         │
 │           │                                        v     checkpoints  │
 │           │                                        │                  │
 │           └────────────────────────────────────────┼──> [5] PLAN      │
 │               ticket + research + design + outline─┘      │           │
 │                                                           │ tactical  │
 │                                                           │ details   │
 │                                                           v           │
 │                                                      IMPLEMENT        │
 └─────────────────────────────────────────────────────────────────────────┘
```

## The 5 Commands

### Step 1: `/qrspi_questions` — Generate Research Questions

**Input:** Ticket/task description
**Output:** 5-12 objective research questions
**Key insight:** Acts as a "query planner" — translates the ticket into questions that touch all relevant code areas WITHOUT revealing implementation intent.

```
 TICKET: "Add email notification channel"
                │
                v
 QUESTIONS (no implementation intent leaked):
   1. How does the notification system work? Trace from creation to delivery.
   2. What types/interfaces exist for notification channels?
   3. How do existing channels (SMS, push) handle message formatting?
   4. What test patterns exist for notification channels?
```

### Step 2: `/qrspi_research` — Objective Codebase Research

**Input:** Questions ONLY (ticket is hidden)
**Output:** Facts-only research document
**Key insight:** The model cannot inject opinions about implementation when it doesn't know what you're building.

```
 ┌──────────────────────────────┐     ┌──────────────────────────────┐
 │     OLD: Research with       │     │     NEW: Research without    │
 │     ticket in context        │     │     ticket in context        │
 │                              │     │                              │
 │  "The notification system    │     │  "The notification system    │
 │   works like X. To add an    │     │   works like X. Channels     │
 │   email channel, you SHOULD  │     │   register via Registry at   │
 │   use pattern Y because..."  │     │   src/notifications:45.      │
 │                              │     │   Each channel implements    │
 │   ^^^ OPINIONS LEAKED ^^^    │     │   Channel<T>..."             │
 │                              │     │                              │
 │                              │     │   ^^^ JUST FACTS ^^^        │
 └──────────────────────────────┘     └──────────────────────────────┘
```

### Step 3: `/qrspi_design` — Interactive Design Discussion

**Input:** Ticket + Research document
**Output:** ~200 line design document
**Key insight:** Forces the agent to "brain dump" its understanding and ask questions BEFORE writing any plan. You catch bad patterns and wrong assumptions on a 200-line doc instead of a 1000-line plan.

```
 THE LEVERAGE POINT
 ==================

 Old way:  Review 1000-line plan ──> Find bad decision on line 847
           ──> Rewrite plan ──> Review again ──> Implement ──> Surprise!

 New way:  Review 200-line design ──> Catch bad decision in Q&A
           ──> Fix it in 1 message ──> Plan is correct from the start

 200 lines to review vs 1000 lines. Same alignment. 5x more leverage.
```

The design doc captures:

- **Current state** (from research, with file:line refs)
- **Desired end state** (what "done" looks like)
- **Patterns to follow** (confirmed by you — no more copying bad patterns)
- **Patterns to AVOID** (you catch these before they propagate)
- **Design decisions** (explicit, with alternatives considered)

### Step 4: `/qrspi_structure` — Structure Outline

**Input:** Ticket + Research + Design document
**Output:** ~2 page outline of implementation phases
**Key insight:** Forces **vertical plans** (tracer bullets) instead of horizontal plans. Like C header files — just signatures and types, not the full implementation.

```
 HORIZONTAL (wrong):              VERTICAL (right):
 ┌─────────────────────┐          ┌─────────────────────┐
 │ Phase 1: ALL DB     │          │ Phase 1: Feature A   │
 │ Phase 2: ALL API    │          │  DB + API + UI slice │
 │ Phase 3: ALL UI     │          │  (testable!)         │
 │ Phase 4: ALL Tests  │          │                      │
 │                     │          │ Phase 2: Feature B   │
 │ Can't test until    │          │  DB + API + UI slice │
 │ Phase 4. If Phase 3 │          │  (testable!)         │
 │ breaks, where's the │          │                      │
 │ bug? Good luck.     │          │ Phase 3: Polish      │
 │                     │          │  Edge cases + tests  │
 │                     │          │                      │
 │                     │          │ Each phase testable. │
 │                     │          │ If Phase 2 breaks,   │
 │                     │          │ Phase 1 still works. │
 └─────────────────────┘          └─────────────────────┘
```

### Step 5: `/qrspi_plan` — Tactical Implementation Plan

**Input:** Ticket + Research + Design + Structure Outline
**Output:** Full implementation plan
**Key insight:** This is now a tactical document for the executing agent. All human alignment happened in steps 3-4. Just spot-check this — save deep review for the actual code.

```
 Design decisions?  Already made in Step 3. ✓
 Phase ordering?    Already approved in Step 4. ✓
 Bad patterns?      Already caught in Step 3. ✓
 Vertical slices?   Already enforced in Step 4. ✓

 This plan just fills in the tactical details.
 Don't deeply review it. Read the code instead.
```

## Instruction Budget Comparison

```
 OLD monolithic plan prompt:
 ┌────────────────────────────────────────────────┐
 │ ████████████████████████████████████████████    │  85 instructions
 │ Context gathering ████████                     │
 │ Research ██████████████                        │
 │ Design options █████████  (often skipped)      │
 │ User feedback ██████████  (often skipped)      │
 │ Structure outline ████████ (often skipped)     │
 │ Plan writing ██████████████                    │
 └────────────────────────────────────────────────┘

 NEW qrspi (5 separate prompts):
 ┌──────────┐ ┌──────────┐ ┌────────────┐ ┌───────────┐ ┌──────────┐
 │ ████████ │ │ ████████ │ │ ██████████ │ │ █████████ │ │ ████████ │
 │ ~15 inst │ │ ~15 inst │ │ ~20 inst   │ │ ~18 inst  │ │ ~15 inst │
 │questions │ │ research │ │ design     │ │ structure │ │ plan     │
 └──────────┘ └──────────┘ └────────────┘ └───────────┘ └──────────┘

 Each well within the ~150-200 instruction budget.
 No steps get skipped. No magic words needed.
```

## Usage

### Quick Start

```bash
# Step 1: Generate research questions from your ticket
/qrspi_questions path/to/your-ticket.md

# Step 2: START A FRESH SESSION — paste only the questions, not the ticket
/qrspi_research
# paste the questions from step 1

# Step 3: Design discussion (interactive)
/qrspi_design
# provide: ticket path + research doc path

# Step 4: Structure outline (interactive)
/qrspi_structure
# provide: ticket + research + design doc paths

# Step 5: Write tactical plan
/qrspi_plan
# provide: ticket + research + design + outline paths
```

### Important: Fresh Context Windows

Each step should ideally run in a **fresh Claude Code session**. This is critical for Step 2 (research) where the ticket must not be in the context. For other steps, fresh sessions keep the instruction count low and prevent context window bloat.

```
 Session 1: /qrspi_questions ──> copy questions
 Session 2: /qrspi_research  ──> paste questions only
 Session 3: /qrspi_design    ──> ticket + research paths
 Session 4: /qrspi_structure ──> ticket + research + design paths
 Session 5: /qrspi_plan      ──> all artifact paths
```

## Installation

Copy the `.claude/skills/` directory into your project:

```bash
cp -r skills/qrspi_* /path/to/your/project/.claude/skills/
```

Or clone this repo and symlink:

```bash
git clone https://github.com/jaeyunha/QRSPI-workflow.git
ln -s /path/to/QRSPI-workflow/skills/qrspi_* /path/to/your/project/.claude/skills/
```

## Task Directory Convention

All QRSPI artifacts for a task are colocated in a single directory, making it easy to track work and find related docs:

```
thoughts/shared/tasks/ENG-XXXX-short-description/
├── ENG-XXXX-01-questions.md
├── ENG-XXXX-02-research.md
├── ENG-XXXX-03-design.md
├── ENG-XXXX-04-structure.md
└── ENG-XXXX-05-plan.md
```

- Ticket number prefix keeps files sorted and traceable
- Step numbers (`01`-`05`) show the workflow order
- Everything in one directory — no hunting across `research/`, `designs/`, `plans/`

## Adapting to Your Project

The skills are project-agnostic. You may want to customize:

1. **Task directory path** — Default: `thoughts/shared/tasks/ENG-XXXX-description/`, using the [thoughts system](https://github.com/humanlayer/humanlayer/tree/main/hlyr) created and open-sourced by the HumanLayer team as a central document system for AI workflows. If you prefer a different convention, change the base path in each SKILL.md to match your project structure.
2. **Ticket prefix** — Default uses `ENG-XXXX`. If your project uses a different prefix (e.g., `PROJ-`, `FE-`), the skills will follow whatever convention you use.
3. **Sub-agent types** — The research step references `codebase-locator`, `codebase-analyzer`, and `codebase-pattern-finder` agents from [HumanLayer's agent definitions](https://github.com/humanlayer/humanlayer/tree/main/.claude/agents). If your setup uses different agent types, update `qrspi_research/SKILL.md`.
4. **Verification commands** — The plan template uses `make check`, `make test`, `make build`. Update to match your project's commands.
5. **Implementation** — QRSPI covers planning (steps 1-5). For implementation, you can find all of HumanLayer's Claude Code custom commands — including `/implement_plan` for executing plans — at [HumanLayer's commands](https://github.com/humanlayer/humanlayer/tree/main/.claude/commands).

## Philosophy

This workflow is built on three principles from Dex Horthy's talk on evolving RPI:

1. **Don't use prompts for control flow** — Use control flow for control flow. If-statements are more reliable than hoping the model follows instruction #67 out of 85.
2. **Don't outsource the thinking** — The engineer makes every design decision. The AI presents options and does the mechanical work. The design discussion is where you catch 80% of the problems.
3. **Seek leverage** — Review 200 lines of design doc, not 1000 lines of plan. Catch bad patterns before they're in the code. Use vertical phases so you can test incrementally.

## Credits

- Talk: [Dex Horthy — Evolving RPI to QRSPI](https://www.youtube.com/watch?v=99Kxkemj1g8)
- Talk: [Dex Horthy — 12 Factor Agents](https://youtu.be/c630qv03i8g)
- Workflow methodology: [Dex Horthy](https://x.com/dexhorthy) and the [HumanLayer](https://humanlayer.dev) team
- Original RPI framework: HumanLayer's open-source Claude Code commands
- "12 Factor Agents" and context engineering: Dex Horthy
- "Do not outsource the thinking": Jake from Netflix
- "Design Concept": Matt Poco
- Instruction budget research: [Kyle](https://humanlayer.dev/blog) citing Arxiv research on LLM instruction following

## License

MIT