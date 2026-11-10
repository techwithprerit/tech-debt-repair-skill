# The context layer

The unglamorous part that matters most. It is a folder of plain files in a git repository describing the estate as it is, refreshed on a schedule by a read-only agent, and read by every planner, specialist, verifier and brief. One investment, many consumers; it is also the telemetry mesh's vocabulary.

## What goes in

```
context/
├── README.md                 # scope rule, refresh cadence, how to read the files
├── services/<name>.md        # owner, environment, dependencies, runbook link, decommission status
├── databases/<name>.md       # engine, version, host, tenancy (which databases share this host), users, 7-day peaks, replicas
├── caches/<name>.md          # engine, size, clients, auth, 7-day peaks
├── messaging/<topic-or-bus>.md  # producers, consumers, retention, lag baseline
├── dns/aliases.csv           # alias → target, last verified
├── alerts/<rule>.md          # rule, threshold, owner, dashboard, runbook, 30-day firing summary
└── mappings/lookalikes.csv   # explicit table for names that differ by one token
```

Each file states its sources (which read tool, which timestamp) so a verifier can re-derive it.

## What stays out

- Ticket history. Context matters more than tickets; link to the ticket system instead.
- Anything an agent inferred rather than read. If a field cannot be read from a source of truth, it is left empty and listed in the refresh report.
- Credentials of any kind.

## Refresh

- A read-only agent regenerates the generated files every few days and opens a pull request touching only those files.
- Curated files (the scope rule, the look-alike table, conventions) change by a normal human pull request.
- The refresh report lists fields it could not read; that list is a backlog of missing sources of truth, which is itself a tech-debt inventory.

## Why plain files

They diff. A pull request that changes a database's host or a service's owner is visible to humans, reviewable, and bisectable when something goes wrong. Databases and knowledge graphs can come later; the first version is a folder.
