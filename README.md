# Hugin

**An AI agent that reports like an owner, not a status bot.**

Huginn was Odin's raven of *thought* — it flew the whole world each day and returned with only what the Allfather needed to hear. Hugin is the same discipline, packaged as a portable agent skill: work independently, surface only what matters, escalate only what deserves a decision, and learn the threshold from the person it reports to.

> Chinese version: [README.zh-CN.md](README.zh-CN.md)

---

## The problem

Give an AI agent a multi-step task and it tends to fail in one of two directions:

- **Over-reporting.** It stops every few minutes to ask about reversible, low-stakes choices. Progress becomes a conversation.
- **Under-reporting.** It silently makes a scope or architecture decision that belonged to you, and you find out at the end — or never.

Both failures are the same failure: the agent has no calibrated sense of *who decides what*, *what is worth an interruption*, and *what a manager actually needs to stay in control*.

Hugin is a governance layer for that calibration. It is not a task framework, not a planner, not a tool. It is a small, strict protocol that changes how an agent behaves at every decision boundary.

## What it does

- **Proceeds independently (A)** on routine, reversible work — no permission-seeking.
- **Asks for high-leverage help (B)** only when one small human action would save substantial time, cost, risk, or rework. Otherwise it keeps going.
- **Escalates real decisions (C)** — goal changes, scope shifts, acceptance-criteria changes, consequential tradeoffs — with options, a recommendation, and the cost of delay.
- **Declares true blockers (D)** — and distinguishes "impossible without you" from "merely slower without you".
- **Translates technical failure** into what the stage was meant to achieve, what actually happened, what it means for the outcome, and what happens next.
- **Learns your threshold.** Repeated corrections and explicit direction become durable rules, stored separately from the core protocol.

## The four modes

| Mode | Trigger | Behavior |
| --- | --- | --- |
| **A — Proceed** | Execution can continue; goal, constraints, and risk unchanged | Acts, reports at the next meaningful checkpoint |
| **B — Help** | Not blocked, but a small human action creates large leverage | Asks concisely, continues on the best fallback if help is unavailable |
| **C — Decision** | The choice changes goals, scope, constraints, acceptance criteria, priorities, cost, or risk ownership | Pauses only that decision; states options, recommendation, consequence of delay |
| **D — Blocked** | Required information, permission, credentials, or resource is genuinely unavailable | States exactly what is missing and the smallest action that unblocks it |

Classification applies to the *affected issue*, not the whole task — unaffected work continues. Conditions are re-checked, so B becomes D only when the optional help becomes genuinely required. C stays distinct from D because a decision and a missing dependency require different human actions.

## Reporting at the right resolution

Most agent output fails because it is written at the executor's level of detail and read at the manager's level of interest. Hugin defines three levels and starts at the lowest useful one:

1. **Manager view (default)** — stage, outcome, meaningful issue, impact, next action, and whether you are needed.
2. **Technical lead view** — root cause, mechanism, alternatives, tradeoffs. Used when a decision needs it.
3. **Execution view** — files, functions, identifiers, logs, stack traces, commits. Used when requested or when evidence must be auditable.

Reports can be six labeled lines or two sentences; the protocol prefers natural prose and forbids hiding failed, timed-out, blocked, or unrun work behind optimistic wording.

## Learning loop

The skill separates **stable core** from **mutable experience**:

- `SKILL.md` holds the protocol: priority order, A/B/C/D, reporting, learning. Ordinary feedback never rewrites it.
- `references/experience-rules.md` holds what it learned from *you* — candidate rules first, promoted to active only on explicit direction ("always", "from now on"), repeated correction, or a clearly stable working style.

The adaptive layer may refine thresholds, reporting depth, timing, and delegation preferences. It may never relax safety, authorization, explicit task constraints, truthfulness, or acceptance standards.

Priority order when rules collide:

1. Explicit current instruction
2. Safety, authorization, hard constraints
3. Current task requirements
4. Stable A/B/C/D rules
5. Active experience rules
6. Default behavior

## Quick start

Hugin is plain Markdown — no scripts, no dependencies, no network access, no MCP servers. Any agent runtime that loads skills from a directory can use it.

**Trae** (user-level, available in every project):

```text
~/.trae-cn/skills/hugin/          # folder name must match `name` in SKILL.md
```

**Codex / WorkBuddy-style skill directories** work the same way — copy the folder into the runtime's skills path.

Then either invoke it explicitly:

> Use hugin for this task. Classify reporting and escalation as A/B/C/D.

or let it load automatically on delegated, multi-step, and status-reporting work (see the `description` field in `SKILL.md`).

## Example

> **You:** Migrate the ingestion pipeline to the new schema. Keep the old path working until Friday.
>
> **Agent (A):** Migrated three of five stages; tests green. Continuing with the remaining two — no decision needed from you.
>
> **Agent (B):** Stage four needs a sample of production traffic to validate. If you can drop a 30-minute capture into `inbox/`, I can validate in one pass; otherwise I will synthesize traffic and flag the coverage gap.
>
> **Agent (C):** Stages one to three are done. Stage five requires dropping the legacy fallback, which changes the Friday constraint you set. Options: keep the fallback (two extra days), or drop it and accept a short rollback window. I recommend keeping it; the rollback risk is asymmetric. This needs your call before tomorrow's deploy.
>
> **Agent (D):** Cannot proceed: the staging credentials expired and I have no path to rotate them. Reissuing a token for `ci-bot` unblocks everything.

## Repository layout

```text
hugin/
├── SKILL.md                        # stable core protocol (priority, A/B/C/D, reporting, learning)
├── references/
│   ├── experience-rules.md         # mutable layer, grown from your feedback (starts empty)
│   └── reporting-guides.md         # B phrasing, leverage threshold, pre-report checklist
├── agents/
│   └── openai.yaml                 # display metadata for skill catalogs
├── README.md
├── README.zh-CN.md
└── LICENSE                         # MIT
```

## Design principles

- **Human attention is a resource.** An interruption has a cost, and the skill only spends it when the return is disproportionate.
- **Authority is granted, not assumed.** Own execution within the authority already given; never expand it quietly.
- **Silence is a claim.** Not reporting a material problem is a statement that there is none — so it must never be untrue.
- **Thresholds are personal.** The right amount of reporting is a property of the manager, not of the model, so the protocol must be able to learn it.
- **Core rules stay frozen.** Adaptation happens in an append-only experience layer, so behavior cannot drift into something you did not approve.

## Compatibility

Runtime-agnostic and model-agnostic. Works wherever a skill is a directory containing a `SKILL.md` with frontmatter. Frontmatter uses `argument-hint` and `license`; WorkBuddy-specific fields were dropped during adaptation. Project-level `AGENTS.md` and any fail-closed gates in your repository outrank this skill.

## Credits

Originally authored as a WorkBuddy skill; this distribution is a minimal port that preserves the A/B/C/D rules, priority order, reporting model, and learning mechanism unchanged. Named after Huginn, Odin's raven of thought.

## License

MIT — see [LICENSE](LICENSE).
