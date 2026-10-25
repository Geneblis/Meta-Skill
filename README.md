# Meta Call Skills

`using-meta-skills` is a **skill router** for coding agents (built and tested on Claude Code). It reads your request, looks at the skills you have installed, picks the smallest set that covers the task, invokes them, and then does the work.

**It does nothing by itself.** It has no coding method of its own. Its only job is to govern your other skills: choose which ones run, in what order, and which ones stay out. The result is a more efficient programmer: the right methodology for each phase of the task, without loading every skill into context.

## What it governs

It can call **any skill you have installed** that is listed in the agent's skill catalog. The intent map in [`using-meta-skills/SKILL.md`](./using-meta-skills/SKILL.md) gives examples, but the router is not limited to them: a skill you install later is picked up from its name and description.

Without other skills installed, the router has nothing to route to. Install it together with the skill packs below.

## Requirements

### 1. An agent host that can list and invoke skills

Claude Code works out of the box. Other hosts need a way to list installed skills and a way to invoke them.

### 2. Companion skills (required set)

This is the set the router was built and tested with. Installing the packs are a must; the router uses whatever is present.

| Pack | Source | Install (Claude Code) | Used for |
|---|---|---|---|
| **agent-skills** (Addy Osmani) | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | `/plugin marketplace add addyosmani/agent-skills`, then `/plugin install agent-skills` | Spec, planning, incremental implementation, TDD, debugging, review, performance, security, docs |
| **superpowers** (Jesse Vincent / obra) | [obra/superpowers](https://github.com/obra/superpowers) | Plugin or copy skills to `~/.claude/skills/` | Verification before completion, finishing branches, git worktrees, subagent work, receiving review, writing skills |
| **Matt Pocock skills** | [mattpocock/skills](https://github.com/mattpocock/skills) | Copy skills to `~/.claude/skills/` | Codebase design, domain modeling, prototype, research, PR writing; `to-spec`, `to-tickets`, `grill-with-docs` (user-only) |
| **ponytail** | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) | `/plugin marketplace add DietrichGebert/ponytail`, then `/plugin install ponytail` | Simplest working solution, over-engineering review |
| **caveman** | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) | `/plugin marketplace add JuliusBrussee/caveman`, then `/plugin install caveman` | Short, low-token answers (output style only) |

Install commands can change; check each repository's README for the current steps.

These packs is are stricly required. With none of them, the router only answers "no matching skill" and you get plain agent behavior.

### 3. Install this skill

Copy the `using-meta-skills` folder into a skill directory your host reads, for example `~/.claude/skills/using-meta-skills/`. Keep `SKILL.md` inside the folder; its `name` must match the folder name.

## How to use it

- Ask for a task as usual. The skill description tells the agent to use the router at the start of non-trivial technical tasks.
- Or call it directly: `/using-meta-skills <task>`.
- To make it the default, add one line to your `CLAUDE.md`: `Use the using-meta-skills skill before non-trivial technical tasks.`

## Core set

For any task that writes or changes code, the router invokes the most universal skill of each pack **together, in one batch**, then adds phase skills on top:

| Pack | Skill |
|---|---|
| caveman | `caveman` |
| ponytail | `ponytail` |
| agent-skills | `incremental-implementation` |
| Matt Pocock | `codebase-design` |
| superpowers | `verification-before-completion` |

Not-installed or already-active skills are skipped. Discussion-only tasks get just `caveman` and `ponytail`. Your request wins: "no caveman" or "detailed answer" drops `caveman`.

## How it stays efficient

- **Core set plus smallest phase set.** Each invoked skill loads its full text into context. After the core set, the router picks one skill per phase and adds a second only when it covers a real gap.
- **Optional methods stay conditional.** `brainstorming` is for open-ended exploration; `systematic-debugging` is an alternative debugging workflow; `source-driven-development` is for work that needs traceable source evidence; and `lean-build` is for explicitly requested lean MVPs or prototypes. Each is used only if installed and applicable.
- **Avoid competing workflows.** Do not combine `brainstorming` with another discovery workflow or `systematic-debugging` with another debugging workflow. Use `lean-build` to keep an explicitly requested MVP small, but skip it if it duplicates or conflicts with the selected implementation workflow. Source-driven work may complement another phase only when it adds evidence traceability rather than repeating normal repository research.
- **Metadata first.** It decides from skill names and descriptions, not by reading every `SKILL.md`.
- **No recursion.** It never calls itself, never calls another router (such as `using-agent-skills`), ignores "pick a skill" steps inside invoked skills, and invokes each skill at most once per task.
- **No double activation.** Always-on modes started by hooks (`caveman`, `ponytail`) are not invoked again.
- **Small tasks skip routing.** Obvious one-step tasks get no skills.

## User-only skills

Some skills (for example `to-spec`, `to-tickets`, `grill-with-docs`, `improve-codebase-architecture`) set `disable-model-invocation: true`. The agent cannot run them. When one fits, the router tells you the slash command to run and continues with the best alternative it can invoke.

## What it does not do

- It does not bundle or install skills.
- It does not assume skills in its intent map are installed.
- It does not do the task's methodology itself; the invoked skills do.
- It does not invoke itself or other routers.

See [`using-meta-skills/SKILL.md`](./using-meta-skills/SKILL.md) for the full workflow and rules.
