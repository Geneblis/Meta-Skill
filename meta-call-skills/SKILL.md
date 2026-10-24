---
name: meta-call-skills
description: "Automatically select and invoke relevant installed skills for coding and technical tasks, including investigation, implementation, debugging, testing, review, documentation, and planning. Use when the user asks to choose or combine skills, or configure this as the agent's default skill router. Requires the agent to discover and invoke installed skills; no specific companion skill is required."
---

# meta-call-skills — intelligent skill router

## Purpose

Select and **use** existing skills based on the user's actual intent. Do not replace, duplicate, install, modify, or simulate skills unnecessarily. This is an **agent procedure**, not a program with magical access to every tool or a universal hook.

## Required workflow

1. **Analyze the request:** Identify the main goal, domain, work phase (discovery, design, implementation, debugging, review, delivery), constraints, and requested artifacts. Explicitly determine whether the user wants discussion only or wants work performed.
2. **Discover available skills:** Prefer the host's native inventory, metadata, and descriptions; inspect known skill directories if permitted. `npx skills ls -g` is optional and may **not** list native plugins. Never claim a skill exists just because it appears in this document.
3. **Select all applicable skills:** Identify every available skill that materially helps with the user's goal or a required stage of the work, and use all of them. Do not impose an arbitrary limit on the number of skills. Do not invoke skills that are merely adjacent, redundant, incompatible, or irrelevant; when skills offer alternative methods for the same stage, choose the one(s) that add distinct value without conflicting.
4. **Check precedence:** System instructions, the user's request, repository rules, and agent limitations take precedence. If skills conflict, follow the explicit request and the more specialized skill; never execute incompatible workflows simultaneously.
5. **Invoke all applicable skills:** Use the host's skill-invocation mechanism for every relevant skill identified during request analysis, including skills for distinct phases such as design, implementation, testing, and review when those phases are part of the task. Do not stop after invoking only a primary skill. If the skill catalog is empty, or a specialized skill needed for the task is unavailable, ask the user whether they want the necessary skill(s) installed. If no specialized skill is relevant, handle the task directly without an install prompt. If installation is unavailable, not permitted, or declined, proceed with a general approach when possible; do not claim a skill was invoked.
6. **Do the task:** Skills are not the deliverable. Provide the requested code, plan, analysis, tests, or documentation. Validate results with available tools.
7. **Be concise by default:** If the `caveman` skill is installed, use it for the default response style: brief, direct, and low on unnecessary wording. If it is not installed, simply respond concisely without treating it as a required dependency. If the user asks for a non-brief response or an in-depth explanation, do not use Caveman for that response. Caveman affects **output style only**, not code quality or performance. When useful, mention only the skills actually used and why. Do not produce a routing report for trivial requests.

## Intent map — examples, not guarantees of installation

| Request | Candidate skills | Suggested order |
|---|---|---|
| New idea / game mechanic / design | `brainstorming` (obra), `grill-me` (mattpocock), `grill-with-docs` (mattpocock) | Brainstorm → refine, if needed |
| Vague requirements / understanding intent | `interview-me` (addyosmani), `grill-me` | Use applicable skills when each adds distinct value; ask focused questions |
| Specification and execution plan | `to-spec`, `to-tickets` (mattpocock), `spec-driven-development` (addyosmani) | Use all applicable skills for requested, distinct stages; avoid conflicting duplicate methods |
| Complex code / simplification | `ponytail` (DietrichGebert), `codebase-design` / `improve-codebase-architecture` (mattpocock) | Use all that address distinct needs; diagnose → simplify → validate |
| Difficult bug debugging | `diagnosing-bugs` (mattpocock), `systematic-debugging` (obra, if available) | Use applicable skills that add distinct value; reproduce first, then fix |
| Tests and regressions | `tdd` (mattpocock), `test-driven-development` (addyosmani) | Use applicable skills without duplicating or conflicting TDD workflows |
| Code/PR review | `code-review` (mattpocock), `code-review-and-quality` (addyosmani), `ponytail-review` (ponytail, if available) | Use all applicable review skills that provide distinct, compatible coverage |
| Technical documentation | `research` or `grill-with-docs` (mattpocock), installed and verified documentation skills | Consult the actual code and write the docs |
| Short answers / fewer tokens in communication | `caveman` (JuliusBrussee) | Default response style, unless the user requests detail or a non-brief answer |
| Small, obvious task | None | Handle directly; do not prompt to install skills that are not relevant |

**Note:** `Code Simplification` is an intent category, **not a confirmed skill name** in this set. Check the actual catalog before using a skill. `Caveman` compresses **output language**; it does not optimize algorithms, RAM, frame time, or architecture. For performance work, profile and test first.

## Selection algorithm

For each skill actually available, internally assess:

- **Fit (0–3):** Does it specifically match the goal?
- **Phase (0–2):** Does it match the current stage of work?
- **Complementarity (0–2):** Does it cover a gap left by the primary skill?
- **Cost/conflict (0–3):** Does it repeat a methodology, create unnecessary interviews, consume context, or alter scope?

Use the score to determine which skills are genuinely applicable, not to enforce a quota. Invoke every skill with meaningful fit and phase relevance unless it is redundant, conflicting, unavailable, or disallowed. Never let this score replace judgment or explicit instructions.

### Example: "simplify this code and make it easier to maintain"

1. Inspect the code and tests; understand dependencies and current behavior.
2. If installed, use `ponytail` to reduce accidental complexity.
3. If there is a genuine structural problem, use `codebase-design` or `improve-codebase-architecture`; **do not** invoke both unnecessarily.
4. Use review and tests if available to preserve behavior.
5. Use `caveman` for the response by default, unless the user asks for a detailed explanation or a non-brief answer. This does not change the code.

### Example: "design a complex web feature for recursively nested matrices"

1. Use `brainstorming` to explore the feature's architecture, data model, user interactions, and trade-offs without prematurely generating code.
2. If important requirements or technical constraints remain unclear, use the applicable requirement-clarification skill(s), such as `grill-me` and/or `grill-with-docs`, while respecting the user's preference not to receive too many questions.
3. If the user asks to formalize or implement the feature, use the relevant specification, implementation, testing, and review skills that are available and add distinct value.
4. Use `caveman` for brief responses by default; do not use it when the user requests an in-depth response.

## Negative rules

- Do not invent skills, commands, file names, or execution results.
- Do not load 100 full `SKILL.md` files to decide what to use: read **metadata** first.
- Do not invoke `meta-call-skills` recursively.
- Do not conduct lengthy interviews for simple tasks or when directly asked to implement.
- Do not let persistent communication preferences (`caveman`, `ponytail`, etc.) override the user's requested format or level of detail.
- Do not install packages, run remote code, use hooks, change global settings, overwrite files, make commits, access accounts, or publish anything without authorization for those actions. If the catalog is empty or a needed specialized skill is unavailable, ask whether the user wants the necessary skill(s) installed; installation still requires the appropriate permission and capability.
- Do not activate duplicate plugins from the same package; avoid conflicts between a native plugin and a CLI-installed copy of a skill.
- Do not promise parallel execution, subagents, or background automation if the host does not provide those capabilities.

## Autodiscovery and behavior across agents

- In agents with implicit skill routing, this description lets the agent consider the skill; elsewhere, use a short global rule to call this router before relevant technical tasks.
- If a needed skill is unavailable in the current environment, ask whether the user wants it installed. If the user declines or installation is unavailable, continue with a general approach when possible; do not claim the skill was used.

## Success criteria

The result addresses the original request, uses every relevant and available skill that adds distinct value, avoids redundant or conflicting workflows, and provides validation evidence when required.
