# Agent roles, model routing and the verifier brief

## Roles

| Role | Model | Reads | Produces | Never does |
|---|---|---|---|---|
| Planner | strongest available | the whole estate via read-only tools, the scope rule, the gate table | a plan split by technology and blast radius, with proof criteria | execute changes |
| Specialist (one per technology) | cheapest model you are ≥90% confident can do the slice | its slice of the plan, the shared context layer | findings or a proposed change for its slice | touch another slice |
| Verifier | strongest available | the same raw inputs the planner had, its own check battery, the known-regression list | `GO` / `GO-WITH-CONDITIONS` / `NO-GO` with evidence | read the planner's reasoning |
| Human | | the plan, the verdict | thresholds, scope, go/no-go, the irreversible click | retype what the agents read |

Routing is a deliberate decision, made once per workflow and written down. Re-evaluate it when a cheaper model becomes good enough or a specialist starts failing verification.

## Why the verifier needs a different brief

A reviewer reads the plan and looks for mistakes; it inherits the plan's framing and misses what the plan never considered. A verifier rebuilds the answer from the inputs and diffs the two. It is slower and it is the step that catches the split replica host, the drifted cohort, the service that was decommissioned last quarter.

## Verifier brief template

```
You are the verifier for <workflow>. You have NOT seen the plan's reasoning and must not ask for it.

Scope rule: <one sentence>
Sources of truth: <gate table>
Known past regressions to check for explicitly:
  1. <e.g. a host that appears in more than one wave>
  2. <e.g. an identifier that matches another by prefix>
  3. <e.g. a service marked decommissioned in the catalogue but present in the plan>
  ...

Procedure:
  a. Re-derive the inventory from the sources of truth using only read tools.
  b. Re-derive the plan's key tables (membership, order, mapping) independently.
  c. Diff your tables against the plan's output. Every difference is a finding.
  d. Run the check battery below and record evidence (tool, arguments, result) for each.
  e. Check that nothing outside the scope rule is touched.

Check battery:
  - [ ] every item in the plan exists in the authoritative source
  - [ ] every item in the authoritative source that matches the scope rule is in the plan
  - [ ] no identifier appears in two places where it should appear in one
  - [ ] every irreversible step is marked and has a named human approver
  - [ ] every proof criterion in the plan is measurable with the tools available
  - [ ] <workflow-specific checks>

Output, in this order:
  VERDICT: GO | GO-WITH-CONDITIONS | NO-GO
  CONDITIONS: (if any; each one a concrete action and an owner)
  FINDINGS: one per difference, with evidence
  CHECKS: the battery, each with PASS/FAIL and evidence
```

## Specialist brief skeleton

```
You own <technology> for <workflow>. Read only.
Scope rule: <one sentence>
Your slice: <list of resources>
Context layer: <path or tool> — use it for tenancy (which host holds what), existing users, seven-day peaks.
Deliver: for each resource, current state, proposed action, the proof that the action succeeded, and the rollback.
Stop and report if any source of truth is unreachable. Do not estimate.
```

## Human review that scales

Humans review templates and decisions, not instances. In a pipeline conversion that means reviewing one template per pattern instead of hundreds of pull requests; in an alert review it means one threshold decision per rule, made with the specialist for that technology, with the firing history on screen.
