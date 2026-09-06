# AI Startup Team

A ten-agent startup in a folder. Drop it into any project and Claude Code gains a
CEO, a PM, a designer, engineers, QA, DevOps, a marketer and a finance analyst —
each with its own system prompt, tool allowlist and model, and a delivery lead that
routes work between them.

These are [Claude Code subagents](https://code.claude.com/docs/en/sub-agents):
plain markdown files with YAML frontmatter. No dependencies, no API keys, nothing
to run.

## Install

**Per project** — clone into the project you're working in:

```bash
git clone https://github.com/USER/ai-startup-team.git /tmp/ai-startup-team
mkdir -p .claude/agents
cp /tmp/ai-startup-team/.claude/agents/*.md .claude/agents/
```

**Globally**, for every project on your machine:

```bash
mkdir -p ~/.claude/agents
cp /tmp/ai-startup-team/.claude/agents/*.md ~/.claude/agents/
```

Restart Claude Code, then run `/agents` to confirm they loaded.

## The team

| Agent | Model | Owns | Tools |
|---|---|---|---|
| `delivery-orchestrator` | opus | Decomposing goals, routing work, assembling output | Read, Write, Edit, Glob, Grep, Task, TodoWrite |
| `chief-executive` | opus | Strategy, positioning, tradeoff calls, success metrics | Read, Glob, Grep, WebSearch, WebFetch, Write |
| `product-manager` | sonnet | Requirements, user stories, acceptance criteria, scope | Read, Write, Edit, Glob, Grep, WebSearch, WebFetch |
| `product-designer` | sonnet | Flows, screen states, interaction specs, accessibility | Read, Write, Edit, Glob, Grep, WebFetch |
| `backend-engineer` | sonnet | Schema, APIs, business logic, auth, migrations | Read, Write, Edit, Bash, Glob, Grep, WebFetch |
| `frontend-engineer` | sonnet | Components, state, data fetching, client performance | Read, Write, Edit, Bash, Glob, Grep, WebFetch |
| `qa-engineer` | sonnet | Test strategy, test code, edge cases, ship/no-ship | Read, Write, Edit, Bash, Glob, Grep |
| `devops-engineer` | sonnet | CI/CD, IaC, deploys, observability, cost | Read, Write, Edit, Bash, Glob, Grep, WebFetch |
| `growth-marketer` | sonnet | Positioning, launch, acquisition, funnel | Read, Write, Edit, Glob, Grep, WebSearch, WebFetch |
| `finance-analyst` | sonnet | Unit economics, pricing, runway, build vs buy | Read, Write, Edit, Bash, Glob, Grep, WebSearch, WebFetch |

## Usage

**Let Claude route.** Describe the goal and the orchestrator picks the path:

```
Build a link-shortener with custom slugs, click analytics and a free tier.
```

**Call one agent directly** with an `@` mention:

```
@agent-qa-engineer find the edge cases in src/billing/proration.ts
@agent-finance-analyst should we build our own email sending or use a provider?
@agent-product-designer spec the empty and error states for the dashboard
```

**Run a whole session as one role:**

```bash
claude --agent product-manager
```

## How the pipeline runs

```
chief-executive  →  product-manager  →  product-designer
                                              │
                          ┌───────────────────┴───────────────────┐
                          ▼                                       ▼
                  backend-engineer  ◄── API contract ──►  frontend-engineer
                          └───────────────────┬───────────────────┘
                                              ▼
                                        qa-engineer
                                              ▼
                                       devops-engineer
                                              ▼
                        growth-marketer  +  finance-analyst
```

The orchestrator skips phases a request doesn't need — a bug fix is define → build →
verify, a pricing question never leaves the CEO and finance. See
[`docs/workflow.md`](docs/workflow.md) for worked examples.

## Design principles

Every agent in this repo follows four rules, and contributions should too:

1. **Least privilege.** Each agent gets only the tools its job needs. The CEO can't
   edit code; the designer can't run Bash.
2. **Explicit boundaries.** Every file ends with what the agent must *not* do and who
   to hand off to. This is what stops agents from quietly doing each other's jobs badly.
3. **A named output format.** Structured output is what makes handoffs between agents
   work instead of degrading into prose.
4. **Opinions, not descriptions.** "Every screen has five states" is useful. "You are
   a great designer who cares about quality" is not.

## Writing your own

Copy [`docs/agent-template.md`](docs/agent-template.md) into `.claude/agents/`,
fill it in, and validate:

```bash
claude plugin validate .claude/agents
```

The `description` field is what Claude uses to decide when to delegate, so write it
as a trigger ("Use when…", "Use PROACTIVELY after…"), not as a job title.

## Prior art

Larger community collections worth raiding for specialist roles:
[VoltAgent/awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents) (150+),
[0xfurai/claude-code-subagents](https://github.com/0xfurai/claude-code-subagents),
[rahulvrane/awesome-claude-agents](https://github.com/rahulvrane/awesome-claude-agents).
This repo is deliberately small — ten agents you'll actually use, rather than a
hundred you'll scroll past.

## License

MIT
