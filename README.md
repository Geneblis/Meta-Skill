# Meta Call Skills

`using-meta-skills` routes technical tasks to the smallest useful set of installed skills, then proceeds with the work. It selects methods for the current phase instead of loading a fixed bundle for every task.

## Requirements and installation

Use an agent host that exposes a skill catalog and supports reading or invoking skills. Copy the `using-meta-skills` folder into a skill directory supported by your host, keeping `SKILL.md` inside it.

Install all five companion skill packs before using the router. They are required system dependencies; installation makes their skills available, while routing activates only the skills needed for each task.

| Required pack | Source |
|---|---|
| agent-skills | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) |
| superpowers | [obra/superpowers](https://github.com/obra/superpowers) |
| Matt Pocock skills | [mattpocock/skills](https://github.com/mattpocock/skills) |
| ponytail | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) |
| caveman | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) |

Follow each source's installation instructions for your host. Verify that the installed skills appear in the host's catalog. If a required pack is missing, setup is incomplete: install the missing dependency before treating the router as fully configured.

## Usage

Ask for a technical task normally, or explicitly ask the agent to use `using-meta-skills`. Where slash-command invocation is supported, use `/using-meta-skills <task>`.

For default routing, add this instruction to your host's project instructions:

> Use using-meta-skills before non-trivial technical tasks.

The router respects the host's instruction hierarchy and skills explicitly requested by the user.

## Efficiency model

- **No mandatory activation bundle.** Each optional skill must fill a concrete gap in the current task.
- **Load by phase.** Diagnose first; load implementation or delivery guidance only when those phases become necessary.
- **Metadata first.** Shortlist from the existing catalog, then read selected bodies and check restrictions or dependencies.
- **Reuse context.** Read once while instructions remain available; apply the method repeatedly as needed. Recover missing instructions after compaction.
- **Avoid duplicate methods.** Choose one applicable copy and one method per need. Prevent recursive routing.
- **Keep simple tasks simple.** Skip optional skills when direct execution suffices.
- **Keep style optional.** Concise output is a preference, not proof of total token savings.

Examples: a small text correction needs no optional skill; an unexplained failure may need a debugging skill; architecture guidance becomes relevant when module boundaries are part of the task. These choices remain subject to explicit user and host requirements.

The authoritative routing rules and candidate examples live in [SKILL.md](using-meta-skills/SKILL.md).

## Evaluating changes

Compare the previous router, the revised router, and a baseline without this router on the same task set, model, installed catalog, and host instructions. Include small edits, diagnosis followed by a fix, design-only requests, reviews, missing skills, duplicate skill copies, and context recovery.

Record total input and output tokens, cached tokens separately where available, tool calls, skills loaded, user clarification turns, successful completion, and rework. Repeat cases to account for variation. Evaluate savings only among runs meeting the same acceptance criteria; a shorter failed run is not an improvement.

Static checks can verify document consistency. Actual token savings and routing reliability require execution measurements; neither is established by document size alone.
