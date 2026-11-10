# Pile picker: choosing the first repair

The first pile decides whether the loop survives. Pick for a visible win in one week, not for the biggest debt.

## Scoring sheet

Score each candidate pile 1–5 on every row. Highest total goes first; ties go to the pile with the most read-only work.

| Criterion | 1 | 5 |
|---|---|---|
| Pain is felt weekly | nobody notices | pages someone, or blocks a release |
| Can be finished in a week | multi-quarter | one agent run plus review |
| Reading dominates writing | mostly config changes | mostly inventory, audit, correlation |
| A source of truth exists | tribal memory | a catalogue or API the agent can read |
| Proof is cheap to define | "feels better" | a diff, a parity table, a replay |
| Rollback is repeatable | one-shot, destructive | disable with a label, re-enable later |
| Nobody owns it | clear owner would object | genuinely unowned |

## Good first piles

- The alert rule set nobody trusts (read every rule with its 30-day firing history; propose keep/retire/retune per rule).
- Dashboards nobody opens (read view counts and panel queries; propose archive list).
- Over-provisioned databases and caches (read seven-day peaks; propose right-sizing tickets).
- CI jobs that still run but deploy nothing (read last-success dates and downstream artifacts).
- Repositories in an artifact store with no reads in 90 days.
- DNS records and aliases that resolve to nothing.

## Bad first piles

- Anything whose rollback is one-shot (a storage engine cutover, a destructive schema change). Do these third, with the loop already trusted.
- Anything with an owner who has not agreed to the read. Get the viewer credential first.
- Anything that needs the agent to write on day one.

## The scope rule

Write the scope as a single sentence a stranger could apply: "In region X, on Kubernetes, nothing else." The planner agent quotes it at the top of every plan and the verifier checks that nothing outside it was touched.
