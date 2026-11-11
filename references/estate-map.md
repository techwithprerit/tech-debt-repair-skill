# The estate map and the unit of change

Most infrastructure debt is a missing map. Before any move, consolidation or cleanup that touches state, have the agents draw the map and write down, per kind of state, the unit it changes in and the way back. Both artefacts are plain files, reviewed like code. Template in `templates/estate-map.md`.

## The map, top to bottom

Four layers. The agents fill each from read-only sources; a human signs the scope rule.

1. **Workloads.** Every service in scope that owns or uses state. Source: the service catalogue (authoritative), cross-checked against deployment namespaces and repositories.
2. **The indirection layer.** What the workloads actually address: DNS aliases, service discovery names, connection strings in config. Record every alias, what it resolves to today, and who owns it.
3. **State, by kind.** Relational databases per engine, caches, message topics, object stores. Per item: host or cluster, tenancy (how many schemas or tenants share it), size, replicas, owner.
4. **Consumers of consumers.** Change-data-capture streams, analytics exports, backups and anything else that reads the state without being a service. These belong to other teams and are the items most often forgotten.

Draw it as a picture as well as a table. A map people can point at ends arguments that a spreadsheet prolongs.

### What the indirection layer buys you

If every workload addresses an alias and never a host, moving state becomes "repoint the alias" instead of "redeploy the service". Check this first:

- Any workload that addresses a host directly is a finding. Fix it before the move, not during.
- Aliases that resolve to nothing, or to the wrong side, are the most common blocker in a parity check.
- Look-alike aliases get an explicit mapping table. Never fuzzy-match.

### What reading everything tends to surface

Expect these, and give each a row in the debt ledger:

- **Ownerless assets**: live, replicating, costing money, claimed by no service. They need a decommission check, not a migration.
- **Hidden classes**: a path that the dashboards do not show, such as services reaching a database through a replica host rather than the primary.
- **Hazards with no off switch**: dual primaries, singleton schedulers, split queues. If nothing can make them dormant on the night, they need a code change before it.
- **Phantoms**: decommissioned services that still appear in an inventory column. This is what the input gate is for.

## The unit of change

"Cutover" means something different for every kind of state. Schedule on the unit, not on the count of things inside it.

| Kind of state | Typical unit | Why |
|---|---|---|
| Engines with per-database logical replication | One database | Each database can be promoted on its own |
| Engines that fail over as a cluster | The whole cluster, every schema on it | One operation moves all tenants together |
| Workloads on a shared replica host | The whole host | Everything on it promotes together, so those workloads share a wave |
| Caches behind an alias | One record flip per cache | Data is copied ahead of time, off the critical path |
| Topics mirrored in both directions | None | Producers and consumers can move independently |

For each unit write down four things: the replication mechanism, the time budget per operation, who runs it, and the way back.

## The way back is asymmetric

Record for every unit whether the way back is **repeatable**, **one-shot** or **none**.

- Repeatable (reverse replication is set up as part of the change): you can go back and forward. Agents can validate; the risk is time.
- One-shot (one rollback, then a rebuild measured in days): treat a rollback as "we stay on the old side until another change window". A human owns this call, by name of role, before the night.
- None: it does not go in a wave until someone has found a way back or accepted the risk in writing.

This asymmetry decides wave membership, wave order and which decisions stay human. Put it on a slide; it is the thing engineers in the room will ask about.

## Waves and pilots

- **Order by blast radius**, least first. Follow the traffic curve of the business; the read path customers see goes last.
- **Cap by crew, not by ambition.** A wave's clock is its slowest stream plus a fixed validation window. Set a maximum number of operations per wave and per day from the number of people who can run them.
- **Keep bound units together.** A unit that is split across waves is the most common planning regression; make it a verifier check.
- **Pilot first.** A small batch cut over for real, chosen to prove the machinery rather than to move volume. Then a larger one in working hours with an audience that will tell you when something breaks: internal or business users before end customers.
- **Set the pilot's bar before it runs.** For example, no added latency on service-to-service calls.
- **Freeze what breaks replication.** Schema changes in scope stop until the move is over. Announce it early and often.
- **Drain downstream readers.** Each stream or export on a unit has to drain or re-attach around that unit's change; sequence it with the owning team, smallest first.

## What the agents do, and do not

Agents build the map, run parity checks, propose the waves, audit them and, on the night, probe connections and validate each operation. They do not run the change. Every production action is a person's.
