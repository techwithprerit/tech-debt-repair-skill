# Estate map · <scope>

## Scope rule
<One sentence a stranger could apply.>

## Layers

| Layer | Count | Authoritative source | Cross-check | Findings |
|---|---|---|---|---|
| Workloads that own or use state | | Service catalogue | Namespaces, repositories | |
| Indirection layer (aliases, discovery names) | | DNS / config | Resolution test from inside the platform | |
| State, by kind | | Inventory / cloud API | Replication dashboards | |
| Consumers of consumers (streams, exports, backups) | | Owning team's inventory | | |

## Findings to carry into the debt ledger
- Ownerless assets:
- Workloads addressing a host directly:
- Aliases that do not resolve, or resolve to the wrong side:
- Hidden classes (paths the dashboards do not show):
- Hazards with no off switch:

## Units of change

| Kind of state | Unit | Replication mechanism | Time budget per operation | Run by (role) | Way back (repeatable / one-shot / none) | Bound to (what must share its wave) |
|---|---|---|---|---|---|---|
| | | | | | | |

## Waves

| Wave | Window | Units | Blast radius | Crew | Validation window | Go / no-go owner (role) |
|---|---|---|---|---|---|---|
| Pilot 1 | | | | | | |
| Pilot 2 | | | | | | |
| 1 | | | | | | |

## Freezes and downstream readers
- Frozen until the move is over:
- Streams and exports to drain or re-attach, in order:
