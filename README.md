# Meta Call Skills

`meta-call-skills` is a skill router for coding agents. It analyzes a request, discovers the skills available to the current agent, selects the installed skills that materially help with the task, and invokes them before completing the requested work.

It is intended for automatic skill selection and combination. It can also be configured as the agent's default router for technical tasks. It does not implement the task itself, install skills, or guarantee that every suggested skill is available.

## How to use it

1. Install or place the `meta-call-skills` folder in a skill directory supported by your agent. Keep `SKILL.md` inside the folder; its `name` must match the folder name.
2. Use an agent host that can discover installed skills and invoke them. If the host does not automatically consider skills, add an agent-level instruction to invoke this router before relevant technical tasks.
3. Ask the agent to perform a task, or explicitly invoke `/meta-call-skills` when slash-command invocation is supported.
4. The router will identify the task's goal and phase, inspect the available skill catalog, select applicable skills, respect higher-priority instructions and constraints, and use the selected skills while doing and validating the task.

The router should select skills based on actual availability and relevance. Names in the intent map in `SKILL.md` are examples, not a bundled catalog or a promise that those skills are installed.

## Requirements and companion skills

### Required

- **No specific companion skill is required.** `meta-call-skills` discovers relevant skills dynamically; it does not depend on a fixed set of other skills.
- The agent host must provide a way to discover the skills available to the agent and a way to invoke them. Without those capabilities, the router cannot perform automatic discovery and invocation.
- Install `meta-call-skills` itself in a skill location recognized by the host.

### Optional

- **`caveman`** is an optional companion for the router's default brief response style. If installed, the router uses it for output style unless the user asks for a detailed response. If it is not installed, the router can still perform its core routing workflow and should simply respond concisely.
- Other task-specific skills are only needed when they are relevant to a particular request. For example, a testing skill can help with a testing task, but it is not a prerequisite for using this router. If a relevant skill is missing, the router should ask whether the user wants it installed and proceed with a general approach when possible; it must not claim to have invoked an unavailable skill.

## What it does not do

- It does not bundle or install any companion skills.
- It does not assume that examples in its intent map are installed.
- It does not replace the agent host's skill discovery or invocation features.
- It does not invoke itself recursively.

See [`SKILL.md`](./SKILL.md) for the full workflow, selection rules, examples, and constraints.
