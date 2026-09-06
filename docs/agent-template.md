# Agent template

Copy this into `.claude/agents/<name>.md` and fill it in.

```markdown
---
name: your-agent-name
description: One or two sentences describing WHEN to use this agent, phrased as a trigger. Start with "Use when..." or "Use PROACTIVELY when...". This is the only thing Claude reads to decide whether to delegate here.
tools: Read, Write, Edit, Glob, Grep
model: sonnet
color: blue
---

You are the <role>. <One sentence on what you are accountable for.>

## What you produce
<The concrete artifacts. Be specific — "acceptance criteria in Given/When/Then form",
not "high-quality documentation".>

## Standards
<The opinionated rules. This is the substance of the agent. Each rule should be
something a competent person could disagree with — if nobody could disagree, it is
not telling the model anything.>

## Output format
<A literal template. Structured output is what makes handoffs work.>

## Boundaries
<What this agent must NOT do, and which agent to hand off to instead.>
```

## Frontmatter reference

| Field | Required | Notes |
|---|---|---|
| `name` | yes | Lowercase and hyphens only. Must match nothing else in the folder. |
| `description` | yes | The delegation trigger. Keep all descriptions combined well under 15k tokens. |
| `tools` | no | Allowlist. Omit to inherit everything — but don't; least privilege is the point. |
| `disallowedTools` | no | Denylist, applied after inheritance. |
| `model` | no | `opus`, `sonnet`, `haiku`, `fable`, a full model ID, or `inherit`. |
| `permissionMode` | no | `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, `plan`. Use `plan` for read-only agents. |
| `maxTurns` | no | Stop after N agentic turns. |
| `skills` | no | Preload skill content into the agent's context. |
| `memory` | no | `user`, `project`, or `local` — persistent memory across invocations. |
| `color` | no | red, blue, green, yellow, purple, orange, pink, cyan. |

## Tool sets by agent type

| Agent type | Tools |
|---|---|
| Reviewer / auditor (read-only) | `Read, Grep, Glob` |
| Researcher / analyst | `Read, Grep, Glob, WebSearch, WebFetch` |
| Engineer | `Read, Write, Edit, Bash, Glob, Grep` |
| Writer / documenter | `Read, Write, Edit, Glob, Grep, WebFetch` |
| Orchestrator | `Read, Write, Edit, Glob, Grep, Task, TodoWrite` |

## Common mistakes

- **A description that names the role instead of the trigger.** "Backend expert" tells
  Claude nothing about when to call it. "Use when designing a schema or changing an
  endpoint" does.
- **Flattery instead of instruction.** "You are a world-class engineer who writes
  beautiful code" changes nothing. "No N+1 queries; paginate every list endpoint"
  changes the output.
- **No boundaries section.** Without it, agents wander into each other's work and you
  get three mediocre opinions instead of one good one.
- **Inheriting all tools.** A strategy agent with `Bash` will eventually run something.
- **Overlapping descriptions.** If two agents could plausibly match the same request,
  Claude picks unpredictably. Make the triggers disjoint.

## Validate before you rely on it

```bash
claude plugin validate .claude/agents
```

Files are silently skipped if the opening `---` isn't line 1, if `name` or
`description` is missing, if `name` contains a colon or leads with a hyphen, or if
the YAML doesn't parse.
