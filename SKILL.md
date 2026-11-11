---
name: tech-debt-repair
description: Turn infrastructure tech-debt panic into a weekly repair loop run by AI agents that read, correlate and verify while humans decide. Use this whenever someone wants to inventory, audit, migrate, consolidate, clean up or de-risk legacy infrastructure (old pipelines, untrusted alerts, unowned repos, cloud migrations, cost sweeps, config drift), asks "where do we even start with our tech debt", wants agents to work on a legacy estate safely, or is designing an MCP server, sub-agent team or scheduled brief for ops work. Also use it to map an estate before a move, to plan a cutover in waves with pilots, to build a debt ledger, or to show leadership how much debt was actually retired. Apply it even when the word "tech debt" is never said; "nobody dares touch that" is the trigger.
---

# Tech Debt Repair

Companion skill to the talk *AI at the Door: Turning Tech Debt Panic into Tech Debt Repairs*. It encodes one idea and the practice around it:

> AI's superpower on legacy infrastructure is not writing. It is reading everything at once.

Infra debt stayed unpaid for three reasons, and all three are reading costs: **too hard** (nobody holds the whole map), **too slow** (weeks of archaeology before one safe change), **too costly** (quarters of senior time for zero visible features). Agents collapse the reading cost. The method below spends that saving on repairs without creating new debt, and it draws a hard line: **agents read, correlate and check; humans decide and make the one irreversible click.**

Use this skill as the operating procedure. The reference files add depth; read them when a step says to.

## What counts as debt

Tech debt is the future cost of a shortcut taken to ship today. Treat it as a loan: the **principal** is the shortcut, the **interest** is what every later change costs because of it, and **bankruptcy** is the week in which interest is all the team can afford. Code and architecture debt get the attention; this skill is for the debt that hides in **infrastructure**, in **operations** and in **knowledge** (the map held in the heads of people who have moved on).

Principal is hard to price, so measure interest: hours per week working around the pile, pages traced to it, changes it blocks, what it costs to carry. Keep one ledger row per pile and never delete a row; it retires with a date and a proof. Definitions, the measuring method and the ledger rules are in `references/debt-ledger.md`.

## The five-step loop (the Monday playbook)

Run this once a week. The goal is not one heroic cleanup; it is that interest stops accruing.

1. **Pick one pile.** The alert nobody trusts, the job nobody deletes, the repo with no owner. One pile, small enough to finish in a week, taken from the debt ledger. See `references/pile-picker.md` for the scoring sheet.
2. **Read-only hands.** Give the agent one MCP server (or equivalent typed tool access) with viewer credentials into the systems a human would open for this pile. Read everything before writing a line. See `references/mcp-readonly.md`.
3. **Gate the inputs.** Name the source of truth for every fact the run depends on. If a source is unreachable, the run stops; the agent never substitutes a guess. See "Input gates" below.
4. **Propose, then verify.** One agent proposes the change; a second agent with a different brief re-derives the answer from the same inputs and diffs. Decide what proof means *before* the proposing agent starts. See `references/verification.md` and `references/agent-roles.md`.
5. **Loop it.** Put the same read on a schedule (daily brief or weekly report). Debt that is checked every week stops compounding. See `references/daily-loop.md`.

Close every repair by counting it: objects read, decisions made, things retired, days on the clock. See "Showing the result" below.

## Three rules that keep the kit from becoming new debt

These were written after breaking something, not before. Enforce them in every run.

**Rule 1 · Hard input gates.** No source of truth, no run. If the service catalogue, ticket system or inventory is unreachable or returns partial data, the agent reports the gate failure and stops. A soft gate is how decommissioned services ended up in a migration wave plan: one dashboard column carried a literal "guess" suffix and endpoint aliases were read as service names. The fix was not a smarter agent, it was a harder gate: one system is authoritative, everything else is a cross-check.

**Rule 2 · Read-only by default.** Flag, do not fix. Every inventory, parity check, audit and cost sweep starts with credentials that cannot change anything. Write access is a separate, later decision made per pile, and every write goes through a diff a second agent must approve. In one migration that was otherwise agent-assisted end to end, the only production incident came from the single automated write that had no verifier in front of it: a config change swapped one service's cache endpoint for a different cache with a similar name. Syntactically perfect, wrong.

**Rule 3 · Verification is the decision.** "It ran without errors" is not proof. Before the agent starts, write down what proves success: a diff against a source of truth, a parity table, a replayed pipeline producing identical artifacts, a two-week burn-in. A second agent checks. A human signs the irreversible step.

## The repair kit

Four tools. None of them is a product; each removes one of the three reasons the debt was never repaid.

| Tool | Removes | What it is in practice |
|---|---|---|
| MCPs | too hard | Every system the on-call human opens at 2 AM becomes a typed, read-only tool the agent can call: service catalogue, dashboards and alert rules with firing history, cloud console, tickets and runbooks, the migration tracker. One shared MCP service for the organisation, not one server per team; do not replace the debt with a pile of forks. |
| Sub-agents | too slow | A planner reads the whole estate and splits the work; specialists (one per technology) work in parallel; a verifier with a different brief re-derives the answer. Archaeology in hours instead of weeks. |
| Autonomous workflows | too costly | Ticket in, verified change (or verified "no") out. A context layer of plain files maps service → database → host. A human approves only the step that cannot be undone. |
| Telemetry mesh | the silos | Not a new tool: a shared vocabulary the agent maintains, so an alert arrives with its dashboard, owner, runbook and last incident already attached. The same context layer the workflows use; one investment, two consumers. |

## Agent roles and model routing

- **Planner** (strong model): reads the entire estate, writes the plan, splits work by technology and blast radius.
- **Specialists** (cheapest model you are at least 90% confident can do the task): one per technology; each gets only its slice and the shared context layer.
- **Verifier** (strong model, *different brief*): not a reviewer. It rebuilds the answer from the same inputs with its own check battery and a list of known past regressions, then returns `NO-GO`, `GO-WITH-CONDITIONS` or `GO` with evidence. A verifier of this kind caught a replica host split across three migration waves that four humans had missed.
- **Human**: scope rules, thresholds, go/no-go, rollback calls, the irreversible click.

Give the verifier a brief the planner never sees; a verifier that reads the plan's reasoning inherits its blind spots. Template in `references/agent-roles.md`.

All roles read the same **context layer**: a folder of plain files in git that maps service → database → host, with tenancy, users and seven-day peaks, refreshed by a read-only agent every few days. No ticket history in it; context matters more than tickets. Layout in `references/context-layer.md`. For the numbers, the blunder and the questions audiences ask, see `references/field-notes.md`.

## Input gates

Before any run, fill this table and refuse to start if a row is empty:

| Fact the run depends on | Authoritative source | Cross-check | Behaviour if unreachable |
|---|---|---|---|
| Which services exist and who owns them | Service catalogue | Cloud tags, repo CODEOWNERS | Stop |
| What runs where (service → database → host) | Context layer / inventory | Cloud console | Stop |
| What "done" means for this ticket | The ticket's acceptance criteria | Runbook | Ask, do not infer |
| Current alert rules and 30-day firing history | Monitoring API | Dashboard export | Stop |

Names that look alike (`service-cache` vs `service-global-cache`) get an explicit mapping table. Never fuzzy-match identifiers.

## Map before you move

Any repair that touches state (a migration, a consolidation, a decommission) starts with two artefacts the agents draw from read-only sources. Method in `references/estate-map.md`, template in `templates/estate-map.md`.

- **The estate map**, in four layers: workloads, the indirection layer they actually address (DNS aliases, discovery names), state by kind, and the readers of that state nobody remembers (change-data-capture streams, exports, backups). If workloads address aliases and never hosts, moving state is "repoint an alias" rather than "redeploy a service"; any workload that addresses a host directly is a finding to fix first.
- **The unit of change and the way back**, per kind of state. One engine moves a database at a time and can go back and forward; another fails over a whole cluster with every schema on it and allows one rollback before a rebuild that takes days; caches flip one record with the data copied ahead; mirrored topics impose no order. Schedule on units, not on the count of things inside them, and mark each way back as repeatable, one-shot or none.

Then order waves by blast radius, cap them by the crew that runs them, keep bound units in the same wave, and pilot first: a small batch cut over for real to prove the machinery, then a larger one in working hours in front of users who will tell you when it breaks. One-shot ways back stay a human call.

## Repair recipes

Pick the recipe that matches the pile; each is in `references/repair-playbooks.md` with the proof it needs.

- **Migration parity and wave planning** (cloud-to-cloud, region, major version): estate map and units of change → inventory bundle → read-only parity checks → wave plan iterated with a verifier → pilots → failure playbooks with a do-not-do list. Proof: parity table per cluster, verifier GO.
- **Observability consolidation** (too many tools, untrusted alerts): read every dashboard and alert rule with firing history → one decision per object, reviewed with the specialist for that technology → backup, retire by pausing with a label, burn new rules in on chat for two weeks. Proof: decision log plus burn-in.
- **CI pipeline conversion** (legacy pipelines to a new system): cluster pipelines into patterns → one reviewed template per pattern → convert repo by repo → verify by replaying the same commit through both systems and diffing artifacts and side effects → disable old jobs for 30 days before deletion. Proof: replay diff.
- **Unowned artifact or repo cleanup**: read access logs and configs → owner-or-retire decision per repo → retire by freezing, then deleting after a quarantine. Proof: zero reads during quarantine.
- **Cost sweep**: per-resource utilisation over seven-day peaks → findings with owner and expected saving → tickets, never direct changes. Proof: billing line moves after the owner acts.

## The daily loop

Once a pile is repaired, keep it repaired with a scheduled brief the humans actually read. A good brief is one page, lands at the same time every day, and follows these rules:

- Every alert from the last 24 hours gets a verdict (real, false positive, known batch pattern) with the evidence.
- Every abnormal resource gets a reason, not just a number.
- Cost is read daily with a month-end forecast; weekly views use a window that ends a few days back so billing lag never lies.
- Anomalies that recur at the same time daily are batch jobs: listed, never flagged.
- Every run is archived; every finding is one click from a ticket that follows the team's ticket standard.
- The brief reads through the same read-only tools as the repairs. Nothing in the loop can write.

Template and schedule guidance in `references/daily-loop.md`.

## Output formats

When this skill produces a plan, use this structure:

```
# Repair plan · <pile>
## The debt (one paragraph, no blame)
## Scope rule (what is in, what is out, in one sentence)
## Sources of truth and gates (the table above, filled)
## What the agents will read (systems, credentials, read-only confirmation)
## Proposed change
## Proof of success (decided now, before the run)
## Verifier brief (separate from the above; see agent-roles)
## Human decisions (thresholds, go/no-go owner, the irreversible step and who clicks it)
## Rollback (one shot or repeatable? who calls it?)
## Loop (what gets scheduled afterwards, and when)
```

When this skill produces a result summary, use the scale scorecard in `templates/scorecard.md`: what the agents read, what people decided, what was retired, and the clock.

When this skill produces a verification report, the first line is the verdict (`GO`, `GO-WITH-CONDITIONS`, `NO-GO`), followed by the checks run, each with evidence, and the conditions if any.

## Showing the result

"Is it working" gets a ratio, not an adjective. For each repair count three things and the clock:

- **Read**: every object the agents opened (repositories, aliases, instances, rules, dashboards, pipelines).
- **Decided**: every decision a person made (keep, fix, retire, go or no-go).
- **Retired**: what no longer exists or no longer costs.
- **Clock**: days from first read to proof.

The read-to-decided ratio is the case for the approach: reading everything removes sampling, which is where debt hides, and people still made every decision. Count from the source system and keep the query; say "about" when a headline is a sum of parts; call a saving an estimate until the bill moves; leave unfinished repairs out of the totals. Rules in `references/debt-ledger.md`.

## Anti-patterns to call out

- Starting with writes because reads feel slow. Reads are the whole point.
- One MCP server per team. Fork sprawl is debt with a new name.
- A verifier that reads the planner's reasoning. Give it the inputs, not the argument.
- "The workflow is green" as proof of a converted pipeline.
- Fuzzy-matching names that differ by one token.
- Deleting instead of disabling. Everything retires through a quarantine with a label and a date.
- Filling the brief with numbers but no verdicts. A human should be able to act on every line.
- Skipping the loop. A one-off cleanup is a snapshot; the debt returns.
- Moving state before drawing the map. The move then becomes the inventory, at night.
- Scheduling on schema or object counts when the engine changes a whole cluster at once.
- Treating every rollback as repeatable. Write down which ones are one-shot before the night.
- Going straight to the big night. Pilot on something real and small first.
- Reporting a saving as fact before the bill has moved.

## How to use this skill with people

Frame findings neutrally and never name individuals in a repair plan or a brief; the debt was created by a system, and it is repaired by one. Say the blunder out loud when you have one: the most persuasive artefact in a tech-debt programme is the honest story of the one time the rules were skipped.
