# Verification: deciding what proof means before the run

"It ran without errors" proves that the tool did not crash. Decide the real proof first, write it in the plan, and have the verifier measure it.

## Proof catalogue

| Change | Proof that it worked | Proof that it is safe to make permanent |
|---|---|---|
| Inventory / mapping | independent re-derivation by the verifier diffs clean against the plan | the authoritative source agrees on every row |
| Migration of a datastore | parity table: schema, row counts or checksums, users and grants, replication lag, endpoints, per cluster | application traffic succeeds against the target for the agreed soak period |
| DNS / endpoint change | every alias resolves to the intended target from inside the consumer's network | old target shows zero connections |
| Alert rule retune or retire | simulated firing on the last 30 days matches the intended behaviour | two weeks of burn-in on a chat channel with no missed real incident |
| Dashboard retirement | zero views during the archive period | archive folder deleted only after the period |
| CI pipeline conversion | same commit replayed through old and new pipelines yields identical artifacts and side effects (tags, uploads, notifications) | old job disabled for 30 days with no one re-enabling it |
| Artifact repo cleanup | zero reads during quarantine | deletion after quarantine with a dated label |
| Right-sizing | seven-day peak stays under the new ceiling | the billing line moves in the next weekly report |
| Config change | diff reviewed by a second agent; identifiers matched against an explicit mapping table | canary first; rollback tested before rollout |

## Retire, never delete

Everything retires through a quarantine: pause or disable it, attach a label with the reason and a date, and delete only when the date passes with no objection. Alert rules get a `retired` label and stay paused; dashboards move to an archive folder; CI jobs are disabled; repos are frozen. The agent files the deletion ticket for the future date.

## Burn-in

New or retuned alert rules carry a `review=pending` label and route to a chat channel instead of the pager for two weeks. Promote a rule only when it has fired on something real or stayed silent through a known quiet period.

## The verdict format

```
VERDICT: GO-WITH-CONDITIONS
CONDITIONS:
  1. Two replica hosts listed in wave 2 and wave 4; assign both to wave 2 (owner: planner).
  2. Three CNAMEs resolve to the old region; fix before wave 1 (owner: network).
FINDINGS: ...
CHECKS: 15 run, 13 PASS, 2 FAIL (above)
```

Humans read the first line. The rest is for the record and for the next verifier's known-regression list, which grows by one entry every time a verifier catches something.

## The irreversible step

Mark every step that cannot be rolled back (dropping data, a one-shot storage engine cutover, deleting after quarantine). It needs a named human approver, a written rollback answer (even if the answer is "none; this is one-shot"), and it is never bundled into an automated run with reversible steps.
