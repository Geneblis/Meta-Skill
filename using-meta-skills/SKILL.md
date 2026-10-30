---
name: using-meta-skills
description: "Route non-trivial technical tasks to relevant installed skills. Use when starting technical work or when asked to select or combine skills."
---

# using-meta-skills

Select the smallest useful set of installed skills for the current phase, then do the task. Optimize total effort to complete the request correctly, not response length alone.

## Workflow

1. Identify the requested outcome, current phase, constraints, and whether the user wants discussion or execution. For small, obvious tasks, proceed without optional skills.
2. Use the host's existing catalog of names and descriptions. Shortlist from metadata; do not scan every skill body or assume examples below are installed.
3. Honor explicitly requested skills and applicable higher-priority requirements. Otherwise start with no skills. Add a candidate only when it fits the current phase and supplies a concrete method missing from the skills already active. Prefer one method per phase; add another only for a distinct gap. No pack has a mandatory representative.
4. Read the selected skill using the host's supported mechanism. Batch independent reads for the current phase when supported. Inspect restrictions and required dependencies before proceeding; revise the selection if the body reveals a mismatch or conflict.
5. Perform the work. Reassess routing only when the phase, scope, or evidence changes enough to require another method. Load later-phase skills when needed, not at task start.
6. Validate the result as appropriate and report the outcome. Mention routing only when requested, required by the host, or needed to explain a material limitation.

## Selection and precedence

- Follow the host's instruction hierarchy, including system and developer instructions. This router cannot override higher-priority requirements. Within compatible skill guidance, prefer the user's explicit choice and the more specialized method.
- Evaluate both benefit and cost: context loaded, tool calls, interviews, duplicated work, and expanded scope. A short skill is not automatically the best fit.
- Use one of competing workflows, such as two debugging or TDD methods. Load required non-router dependencies only when applicable; reuse those already available.
- Style skills such as `caveman` are optional unless requested or already active. Preserve requested language, format, and detail. Shorter output alone does not establish lower total cost.
- Use authorization already provided in the conversation. Ask only for missing information or approval required by the host or the action; skill selection does not itself authorize external actions.

## Reuse and loop guards

- Read a skill once while its instructions remain available in context; apply its method whenever needed. Reload only when the relevant instructions are unavailable, such as after compaction, or the source changed.
- Reuse active modes and skills started by hooks. Choose one applicable copy when local and plugin versions duplicate the same skill; prefer an explicit user-selected source.
- Never invoke this router recursively or enter another skill router. Treat downstream requests to select skills as handled here, subject to higher-priority instructions.

## Required installation

All five companion packs listed in [README.md](../README.md#requirements-and-installation) are required system dependencies. Installation is mandatory; activation remains selective. Consult that list during setup or when dependencies appear missing, not on every task. Report missing required dependencies as incomplete setup and direct the user to install them. Do not silently treat a partial installation as a fully configured system.

## Availability

Honor the host's invocation restrictions, including `disable-model-invocation: true` when exposed. For a user-only skill not explicitly invoked, suggest its verified command only if useful and proceed with an allowed alternative. Never claim to have invoked an unavailable skill.

If a required dependency is missing, report it as described above. For skills outside the required packs, mention absence only when it materially limits the result or the user asks. A general approach may keep independent work moving, but does not satisfy missing installation requirements. Do not invent sources or commands, or install skills without authorization.

## Routing examples

These are alternatives, not bundles or installation requirements. Match against the actual catalog.

| Current need | Possible selection |
|---|---|
| Explore an idea | `brainstorming` or `idea-refine` |
| Resolve requirements blocking progress | `interview-me` |
| Write a spec or plan | `spec-driven-development` or `planning-and-task-breakdown`, according to the requested artifact |
| Implement a change | `incremental-implementation`; add a testing method only when needed or required |
| Constrain an MVP | `lean-build` |
| Simplify code | `code-simplification` or `ponytail` |
| Design module boundaries | `codebase-design` |
| Diagnose a bug | `systematic-debugging` or `debugging-and-error-recovery` |
| Review changes | One review skill; `receiving-code-review` for processing feedback |
| Research or document | `research` or `documentation-and-adrs`, according to the deliverable |
| Require traceable sources | `source-driven-development` |
| Investigate performance or security | The relevant specialized skill from the catalog |
| Verify or integrate completed work | `verification-before-completion`; integration guidance only when integration is in scope |
| Write or edit skills | `writing-for-agents` or `writing-skills` |

## Completion

The requested outcome is delivered with appropriate validation. Every optional skill loaded addressed a concrete need; no competing workflow or recursive router added unnecessary work.
