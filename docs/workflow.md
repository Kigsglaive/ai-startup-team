# Workflows

Worked examples of how the ten agents actually get used. The point of each is the
same: run the phases the request needs, and no others.

## 1. New product, end to end

> "Build a link shortener with custom slugs, click analytics and a free tier."

| Phase | Agent | Artifact |
|---|---|---|
| Frame | `chief-executive` | Who it's for, what it replaces, the one success metric, kill criteria |
| Define | `product-manager` | Stories with Given/When/Then acceptance criteria, explicit v1/v2/never |
| Design | `product-designer` | Flows + every screen in five states + tokens |
| Contract | `backend-engineer` | Endpoint shapes published before parallel build starts |
| Build | `backend-engineer` ‖ `frontend-engineer` | Running code on both sides of the contract |
| Verify | `qa-engineer` | Tests against every AC, plus edge cases nobody specified |
| Ship | `devops-engineer` | Pipeline, environments, rollout behind a flag, alerts |
| Launch | `growth-marketer` ‖ `finance-analyst` | Positioning and launch sequence; pricing and unit economics |

The two parallel bars matter. Backend and frontend run together **only after** the
API contract exists — that's the whole reason the contract is its own phase.

## 2. Bug fix

> "Users on the team plan are seeing someone else's invoices."

Skip the pipeline. This is:

1. `qa-engineer` — reproduce it precisely, establish blast radius and severity.
2. `backend-engineer` — fix, with a regression test.
3. `qa-engineer` — verify the fix and check for the same class of bug elsewhere
   (this one is a tenant-scoping bug; there are almost certainly siblings).
4. `devops-engineer` — deploy, and confirm nothing else moved.

A cross-tenant data leak also goes back to `chief-executive` for a disclosure call.

## 3. Should we build it or buy it?

> "Do we build our own email sending or use a provider?"

1. `finance-analyst` — total cost over 24 months both ways, including the engineering
   time to maintain the built version and the deliverability work nobody budgets for.
2. `backend-engineer` — what integration actually costs in effort and lock-in.
3. `chief-executive` — the call, with the belief it rests on written down.

No design, no QA, no marketing. Three agents, one decision.

## 4. Feature request that arrives mid-sprint

> "A customer wants CSV export."

1. `product-manager` — is this in scope, and what does it displace? Write the ACs
   only if it survives.
2. `chief-executive` — if it displaces committed work, this is a tradeoff call, not
   a PM decision.
3. Then the normal build → verify → ship path.

The value here is the *refusal* path. Most feature requests should end at step 1.

## 5. Pre-launch readiness check

Run these three in parallel and reconcile:

- `qa-engineer` — what's covered, what isn't, would you ship?
- `devops-engineer` — is rollback tested, are alerts wired, what's the blast radius?
- `finance-analyst` — what does launch traffic cost at 10× current volume?

Then `chief-executive` makes the go call with all three risks visible.

## Handoff hygiene

The failure mode of a multi-agent setup isn't a bad agent — it's a lossy handoff.
Three rules keep it working:

**Pass artifacts, not summaries.** Give the next agent the actual acceptance criteria
or the actual contract, not your paraphrase of it.

**Write the contract before parallel work.** Any boundary two agents will both touch —
endpoint shapes, prop interfaces, event names, file ownership — gets written down
first. Without it, parallel work produces two incompatible halves.

**Escalate conflicts, don't negotiate them.** When design wants something engineering
says is expensive, that goes to `chief-executive` as a stated tradeoff. Agents
ping-ponging to consensus burns tokens and lands somewhere nobody chose.

## When not to use the team

For a one-line change, a factual question, or anything you could do faster yourself,
just ask Claude directly. Delegation has overhead — each agent starts with a fresh
context and has to be told what it needs to know. Use the team when the work genuinely
spans disciplines, not to make a small task feel important.
