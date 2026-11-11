# Repair playbooks

One recipe per common pile. Each follows the same shape: read everything, propose per object, verify independently, retire through quarantine, then schedule the read.

## 1 · Migration parity and wave planning

Use for cloud-to-cloud, region or major-version moves of databases, caches and message systems.

0. **Estate map and units of change.** Draw the four-layer map and the unit-of-change table before anything else (`estate-map.md`). Fix workloads that address hosts directly; list ownerless assets for a decommission check instead of a migration.
1. **Knowledge bundle.** Agents correlate repositories, deployment namespaces, database schemas, caches and topics into one inventory. Humans supply the scope rule ("this region, on Kubernetes, nothing else") and the authoritative source (the service catalogue).
2. **Parity checks, read-only.** Source versus target per cluster: schema, users and grants, sizes, versions, replication lag, endpoints and aliases, storage headroom. Output a parity table with every gap flagged, never fixed. Full disks and unresolved DNS are the usual blockers.
3. **Wave plan with a verifier.** The planner orders waves by business blast radius. The verifier re-derives membership and order and checks for known regressions (a host split across waves, a decommissioned service present, a cohort that drifted). Iterate until `GO`; seven versions in a week is normal.
4. **Pilots.** A small batch cut over for real to prove the machinery, then a larger one in working hours in front of users who will report breakage quickly. Agree the pilot's bar first (for example, no added latency on service-to-service calls). Freeze schema changes in scope, and sequence the drain of downstream streams with their owners.
5. **Failure playbooks.** For each service and component: failure modes, risks, a decision tree and a do-not-do list. Record which rollbacks are one-shot (typically a MySQL-style cutover) and which are repeatable (typically Postgres with logical replication) because that asymmetry decides the night.
6. **The night.** Agents validate connections and parity live; humans execute the cutover and make the go/no-go and rollback calls. Agents do not write to production during the window.
7. **After.** The same parity check runs on a schedule until decommission. Count the repair on the scale scorecard.

Humans keep: the scope rule, wave order, the one-rollback-no-roll-forward call, go/no-go.

## 2 · Observability consolidation

Use when there are too many tools, dashboards disagree and alerts are ignored.

1. Read every dashboard (queries, panels, view counts) and every alert rule with its 30-day firing history through the monitoring API, read-only.
2. Produce one decision per object: keep, merge, retune (with the new threshold and the simulated firing), retire. Group by technology.
3. Review each group with the specialist for that technology, firing history on screen. Humans own every threshold.
4. Back up everything first. Apply: consolidate folders, archive retired dashboards for 30 days, pause retired rules with a `retired` label, route new and retuned rules to chat with `review=pending` for two weeks.
5. Promote rules after burn-in. Delete the archive after the period.
6. Schedule a weekly alert-noise review that reads firing history and proposes the next retunes.

Target shape: a handful of tools and five golden signals per service, with each alert carrying its dashboard, owner and runbook (the telemetry mesh).

## 3 · CI pipeline conversion

Use for moving legacy pipeline definitions (for example Jenkinsfiles) to a new CI system.

1. **Archaeology.** Sub-agents read every pipeline file and cluster them into patterns: build-and-publish, deploy-to-environment, scheduled job, and so on. Record last successful run and whether anything downstream still depends on the output.
2. **Convert by pattern.** One reviewed template per pattern. Humans review templates, not individual pull requests. Apply the template repo by repo; the specialist fills the per-repo parameters from the old file.
3. **Verify by replay.** Run the same commit through the old and the new pipeline; the verifier diffs artifacts, tags, uploads and notifications. A green run is not proof; an identical artifact is.
4. **Retire.** Disable the old job (do not delete) for 30 days; the agent files the deletion ticket for the date.
5. **Loop.** A weekly read lists pipelines that have not run in 90 days and new pipelines created outside the templates.

## 4 · Unowned artifact or repository cleanup

Use for artifact stores, container registries and package repositories with a decade of unowned content.

1. Read access logs (last read per repo), configs, replication rules and permissions.
2. For each repo: owner from the service catalogue, last read, size, and whether any pipeline references it.
3. Decision per repo: assign owner, freeze, or retire. Freeze means read-only with a label and a date.
4. Quarantine: frozen repos with zero reads for the period are deleted; any read during quarantine reverts the freeze and opens a ticket to the reader's team.
5. Loop: a monthly read of repos with no owner or no reads.

## 5 · Cost sweep

Use after a migration or any time a bill has grown without anyone deciding it should.

1. Read per-resource utilisation over seven-day peaks (CPU, memory, storage allocated versus used, connections) and billing by resource.
2. Findings, each with the resource, the owner, the evidence, the proposed action and the expected saving. Typical finds: storage allocated far above use, legacy machine types peaking under 20% CPU, caches under 10% memory, backup vaults nobody owns.
3. Tickets, never direct changes. The owner acts; the weekly cost report confirms the billing line moved.
4. Loop: daily per-resource trend with month-end forecast; weekly report over a window that ends a few days back so billing lag does not mislead.

## Writing up a repair

Use the plan format in SKILL.md. Add a one-row entry to a running scorecard so the programme is visible:

| Repair | The debt | Kit used | Humans kept | Result |
|---|---|---|---|---|
| | | | | |

The pattern to look for across rows: agents did the reading, the correlating and the checking; humans did the deciding and the one irreversible click. Every time the pattern held, the debt shrank.

Then add the repair to the scale scorecard (`templates/scorecard.md`): objects read, decisions made, things retired, days on the clock. The counting rules are in `debt-ledger.md`.
