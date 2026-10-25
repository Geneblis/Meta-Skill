---
name: using-meta-skills
description: "Skill router. At the start of any non-trivial coding or technical task (investigation, design, implementation, debugging, testing, review, documentation, planning), pick the smallest set of installed skills that covers the task's phases and invoke them; then do the task. Also use when the user asks which skills to use or to combine skills. Does no work by itself and never calls itself or another router."
---

# using-meta-skills — skill router

## Purpose

Pick and **invoke** installed skills that fit the user's intent, then do the work. This skill does nothing by itself: it only decides which other skills run. It does not replace, duplicate, install, or simulate skills.

## Workflow

1. **Analyze the request.** Goal, domain, phase (discovery, design, implementation, debugging, review, delivery), constraints, requested output. Does the user want discussion only, or work done?
2. **Read the catalog.** Use the skill list the host already gives you (names + descriptions). Read **metadata only**; never open full `SKILL.md` files to decide. Never assume a skill exists because it appears in this document.
3. **Select skills.** Start with the core set (below). Then add one phase skill per phase the task actually has, from the intent map. Add a second skill for a phase only when it adds something the first lacks. Each invoked skill loads its full text into context, so every extra skill must earn its cost. Small, obvious task: invoke nothing.
4. **Check precedence.** System instructions > user request > repository rules > skills. On conflict between skills, follow the explicit request and the more specialized skill. Never run two competing methods for the same phase (e.g. two TDD workflows).
5. **Invoke all at once, once each.** Call every selected skill in one batch before starting the work: parallel tool calls in a single message when the host allows it, otherwise back-to-back with no work in between. Each skill at most once per task. Respect the guards below.
6. **Do the task.** Skills are not the deliverable. Produce the code, plan, tests, or docs and validate with available tools.
7. **Report briefly.** Name the skills used and why in one line. No routing report for trivial requests.

## Core set

The most universal skill from each pack. Invoke all installed ones together for every task that writes or changes code. For discussion-only tasks (questions, explanations, reviews without edits), invoke only the style skills (`caveman`, `ponytail`).

| Pack | Skill | Why it is universal |
|---|---|---|
| caveman | `caveman` | Short, low-token answers |
| ponytail | `ponytail` | Simplest working code, no over-engineering |
| agent-skills | `incremental-implementation` | Thin, verifiable slices for any change |
| Matt Pocock | `codebase-design` | Deep modules and clean seams in any code written |
| superpowers | `verification-before-completion` | Evidence before claiming done |

Rules:
- Skip a core skill that is not installed. Do not ask to install it.
- Skip a core skill already active in this conversation (e.g. started by a hook). It counts as invoked.
- The user's request beats the core set: "no caveman" or "detailed answer" drops `caveman`; "quick hack" drops `codebase-design`.
- Phase skills from the intent map come on top of the core set, never instead of it. Do not add a phase skill that repeats a core skill's method.

## Recursion and loop guards

- Never invoke `using-meta-skills` from itself or from a skill it invoked.
- Never invoke another router or meta-skill whose job is choosing skills (e.g. `using-agent-skills`). This router already did that job.
- If an invoked skill says "use a skill router" or "check which skills apply", ignore that step: routing is done.
- Never invoke a skill twice in the same task. If a skill is already loaded in this conversation, reuse it; do not reload it.
- Always-on modes started by hooks (e.g. `caveman`, `ponytail`) are already active. Do not invoke them again.

## User-only skills

Some skills set `disable-model-invocation: true`. The agent cannot invoke them; only the user can, via `/<name>`. When one fits best, tell the user the command in one line (e.g. "Run `/to-spec` to turn this into a spec") and continue with the best model-invocable alternative. Never claim to have run it.

## Missing skills

If a specialized skill the task needs is not installed, say so in one line and suggest its source. Proceed with a general approach. Never install anything without permission.

## Intent map — examples, not guarantees

`(user)` = user-only, suggest the slash command.

| Request | Candidate skills | Order |
|---|---|---|
| New idea / feature design | `idea-refine`, `prototype`, `grill-with-docs` (user) | Refine idea → prototype if unsure |
| Vague requirements | `grill-with-docs` (user) | Few focused questions |
| Spec and plan | `spec-driven-development`, `planning-and-task-breakdown`; `to-spec`, `to-tickets` (user) | Spec → tasks |
| Implementation | `incremental-implementation`, `test-driven-development` | Thin slices, test each |
| Complex code / simplification | `ponytail`, `code-simplification`, `codebase-design`; `improve-codebase-architecture` (user) | Diagnose → simplify → validate |
| Bug | `debugging-and-error-recovery` | Reproduce → root cause → fix → test |
| Code / PR review | `code-review-and-quality`, `ponytail-review`, `receiving-code-review` (for incoming feedback) | One review method plus one simplicity pass at most |
| Docs / research | `research`, `documentation-and-adrs`, `domain-modeling` | Read real code first |
| Performance | `performance-optimization` | Measure → fix → measure |
| Security | `security-and-hardening` | Threat model → fix |
| Finishing work | `verification-before-completion`, `finishing-a-development-branch` | Verify → integrate |
| Writing / editing skills | `writing-skills`, `writing-for-agents` | — |
| Small, obvious task | None | Do it directly |

`caveman` changes **output wording only**. It does not optimize code, memory, or architecture.

## Selection heuristic

For each candidate, judge quickly:

- **Fit:** matches the goal specifically?
- **Phase:** matches a phase this task has?
- **Gap:** covers something the already-picked skills do not?
- **Cost:** repeats a method, adds interviews, consumes context, or widens scope?

Pick it only if fit, phase, and gap are clear and cost is low. Judgment and explicit instructions beat this heuristic.

### Example: "simplify this code"

1. Core set in one batch (`ponytail` and `codebase-design` already cover simplification and structure).
2. Phase skill: `code-simplification` only if the core set leaves a gap.
3. Read the code and tests, simplify, run tests to keep behavior.

No review skill unless asked.

### Example: "design a feature for recursively nested matrices"

1. Discussion only so far: style skills from the core set, plus `idea-refine` to explore the data model and trade-offs.
2. `grill-with-docs` if key constraints are still unclear.
3. If the user asks to build it: full core set plus `spec-driven-development`.

## Negative rules

- Do not invent skills, commands, file names, or results.
- Do not run long interviews for simple tasks or when told to implement.
- Do not let style modes override the user's requested format or detail.
- Do not install packages, run remote code, change global settings, commit, or publish without permission.
- Do not load duplicate copies of a skill (plugin vs. local copy); pick one.
- Do not promise subagents or parallel runs the host does not provide.

## Success criteria

The request is done, the core set plus the fewest phase skills were invoked in one batch, no skill ran twice, no router was re-entered, and validation evidence is shown when needed.
