---
name: agent-creator
description: Create or update Codex custom subagent TOML files under `.codex/agents/` or `~/.codex/agents/`. Use when the user asks to create, design, review, or refine a Codex subagent/custom agent, define agent task boundaries, choose model/sandbox settings, or add preferred skill dependency guidance for runtime use.
---

# Agent Creator

Create Codex custom agents using the official custom agent TOML schema. Keep the file compatible with Codex by writing only supported top-level config keys, and put role boundaries, operating rules, and preferred skill dependencies inside `developer_instructions`.

## Contract

Use this skill to create, update, or review one Codex custom subagent TOML file.

Do not use this skill for normal `SKILL.md` packages; redirect to `skill-builder` mid-task if this comes up during execution. Do not install skills, publish agents, or change global Codex settings unless the user explicitly asks.

Stop and ask when the target agent job, write location, destructive permission, or required credentials cannot be inferred safely.

Stop and ask when:
- The user requests `sandbox_mode = "workspace-write"` but the described agent responsibility is read-only or analytical — confirm intent before writing.
- Skill dependencies are requested but no skill identifiers are given and `find-skills` returns ambiguous candidates.

## Input Contract

Resolve these inputs from the user request or nearby repository conventions:

- Agent name and single responsibility.
- Target path, defaulting to `.codex/agents/` for project-local agents or `~/.codex/agents/` only when the user asks for a global agent.
- Whether the task is create, update, or review.
- Runtime boundary, including read-only versus editing behavior.
- Useful preferred skills, if the agent's job has repeatable capabilities.

If preferred skills are relevant but unspecified, use `find-skills` to propose candidates and ask the user before adding them to `developer_instructions`.

## Output Contract

Produce or report:

- The TOML file path created, updated, or reviewed.
- The official Codex fields used.
- The role boundary captured in `developer_instructions`.
- Preferred skill guidance added or intentionally omitted.
- Validation evidence and any unresolved risks.

Do not claim completion unless the TOML uses supported fields only and the agent has one clear job.

## Workflow

1. Clarify the agent's job, target location, and whether the user wants a new file or an update.
2. Generate or review only official custom agent fields:
   - `name`
   - `description`
   - `developer_instructions`
   - `nickname_candidates`
   - `model`
   - `model_reasoning_effort`
   - `sandbox_mode`
   - `mcp_servers`
   - other supported Codex `config.toml` keys when clearly needed
3. Define the role boundary inside `developer_instructions`.
4. Use `find-skills` to identify useful skills for the agent's job when skill dependencies are relevant.
5. Ask the user which recommended skills should be listed as preferred skills.
6. Add selected skill guidance only inside the `Skill Dependencies` section of `developer_instructions`.
7. Copy [agent-template.toml](./assets/agent-template.toml) as the starting point, fill each section, then remove inapplicable sections. Keep `Mission`, `Scope`, and `Output` for every agent.
8. Verify the TOML contains no custom schema fields before finishing.

## Field Rules

- Use `name` as the Codex agent identifier; prefer snake_case.
- Match the filename to the agent name when practical, using hyphenated filenames if that is the local convention.
- Make `description` human-facing and specific about when to use the agent.
- Keep `developer_instructions` narrow, operational, and enforceable.
- Set `sandbox_mode = "read-only"` for exploration, review, documentation research, or analysis-only agents.
- Use `sandbox_mode = "workspace-write"` only when the agent is expected to edit project files.
- Leave `model`, `model_reasoning_effort`, and MCP settings unset when inheritance from the parent session is sufficient.
- Do not raise global `[agents]` settings such as `max_depth` unless the user explicitly asks.
- Do not add custom fields such as `[skills]`, `recommended_skills`, `scope`, `inputs`, `outputs`, or `boundaries` to the TOML.

## Developer Instructions Contract

Write `developer_instructions` with these sections when applicable. Use [agent-template.toml](./assets/agent-template.toml) as the base — fill each section and remove sections that do not apply. Always keep `Mission`, `Scope`, and `Output`.

```text
## Mission
State the agent's single primary responsibility.

## Scope
Allowed:
- ...

Not allowed:
- ...

## Inputs
List what the parent agent should provide.

## Output
Define the result format the parent agent should expect.

## Stop Conditions
Define when the agent should stop and report instead of continuing.

## Escalation
Define what must be handed back to the parent agent.

## Skill Dependencies
Preferred skills for this agent:
- owner/repo@skill-name: Explain when to use it.

Skill resolution policy:
- Treat listed skills as preferred capabilities, not mandatory startup dependencies.
- Do not install skills during agent creation.
- When a task requires a listed skill and it is not available locally, use find-skills to locate it.
- Runtime installation is allowed only at project level.
- Never perform global skill installation from this subagent.
- If the skill cannot be installed or found, report the limitation and continue best-effort.
```

## Skill Dependency Guidance

Use `find-skills` before adding preferred skills unless the user already gave exact skill identifiers. Present candidate skills with:

- skill identifier
- short reason it fits this agent
- whether it is required for core work or optional for better execution

After the user confirms selections, list them in `developer_instructions` under `Skill Dependencies`. Do not install missing skills during creation unless the user explicitly asks. Do not write `[[skills.config]]` by default; this skill favors instruction-level compatibility over hard binding.

## Red Flags

Observable signs the agent being built has a quality problem — fix before finishing:

- `developer_instructions` has no `Not allowed:` list under `Scope` → the agent has no boundary enforcement.
- `Mission` is more than two sentences → the agent has more than one job; split it.
- `sandbox_mode = "workspace-write"` is set but `Not allowed:` does not explicitly name files or paths the agent must not touch.
- `Output` section is absent or vague ("return the result") → the parent agent cannot reliably summarize or consume it.
- Custom TOML fields appear (e.g., `[skills]`, `boundaries`, `scope`) → Codex will ignore or error on them.
- `Skill Dependencies` lists skills the user did not confirm → remove unconfirmed suggestions.

## Anti-Rationalization

| Rationalization | Reality |
|---|---|
| "The Mission is clear, I don't need a Scope section" | Mission says what the agent does. Scope says what it must refuse. Without `Not allowed:`, any adjacent task can bleed in at runtime. |
| "The agent only does one simple thing, Stop Conditions are obvious" | If they are obvious, write them in one line. If you skip them, the parent agent has no signal to stop delegating when the subagent is blocked. |
| "workspace-write is fine — the agent is smart enough not to touch wrong files" | Smart is not a safety boundary. List the paths or patterns the agent must not touch explicitly in `Not allowed:`. |
| "I'll add a few extra skills in Skill Dependencies just in case" | Only list skills the user confirmed. Unconfirmed skills mislead the runtime about actual capabilities and create false dependencies. |
| "Output doesn't need a format — the parent will figure it out" | The parent agent summarizes subagent output. Without a defined format, summaries drift, evidence gets dropped, and the handoff breaks silently. |
| "This TOML has no unsupported fields, I can see that visually" | Visual review misses subtle cases. Run the validation check — grep for known-invalid keys — before claiming the file is clean. |

## Validation

Before finishing, verify:

```bash
# No custom/unsupported fields in the TOML
grep -En "^\[skills\]|^recommended_skills|^scope\s*=|^inputs\s*=|^outputs\s*=|^boundaries" *.toml && echo "INVALID FIELDS FOUND" || echo "ok"
```

- The agent has one clear job (Mission is ≤2 sentences).
- Read-only agents have `sandbox_mode = "read-only"` and no file-edit instructions in `developer_instructions`.
- Editing agents are scoped to small, defensible changes with explicit path exclusions in `Not allowed:`.
- `developer_instructions` includes explicit allowed and not-allowed work.
- Output expectations are concrete enough for the parent agent to summarize.
- Skill dependencies are only instructions, not custom TOML fields.
- Missing skills are resolved at runtime, project-level only, never globally by default.
- Every listed skill was confirmed by the user before being added.
