# Read-only MCP servers for infrastructure work

The agent gets hands, not screenshots. Every system a human opens during an incident becomes a typed tool the agent can call. Start read-only; write tools are a separate server, a separate credential and a separate decision.

## Checklist before a server goes live

- [ ] Its own service account with viewer-level permissions and nothing else. Treat it like a new hire's first-week access: scoped, logged, revocable.
- [ ] Every tool call is audit-logged with the caller, the arguments and the row count returned.
- [ ] Tools return structured data (ids, names, timestamps, units), never prose or rendered dashboards.
- [ ] Each tool declares which system is authoritative for the fields it returns, so the gate table can be filled.
- [ ] Partial results are impossible to miss: a tool that could not reach its backend raises, it does not return an empty list.
- [ ] Pagination and time windows are explicit arguments; defaults are documented in the tool description.
- [ ] One shared server per system for the whole organisation, versioned and owned. No per-team forks.
- [ ] The server runs behind the same gateway as model calls, so cost and usage are visible in one place.

## The minimum set for a platform team

| System | Read tools worth having |
|---|---|
| Service catalogue | service by name, owner, environment, dependencies, decommission status |
| Monitoring | list dashboards (with view counts), list alert rules, firing history for a rule over N days, run a query |
| Cloud console | list instances/replicas/caches/topics with size, version, machine type; seven-day peak utilisation; billing by resource |
| Tickets and docs | ticket by id, search, runbook by service, incident timeline |
| Inventory / migration tracker | service → database → host mapping, DNS records and aliases, wave assignment |
| Source control | list repos, pipeline files, CODEOWNERS, last commit per path |

## Tool design notes

- Name tools after the question, not the API: `alert_rule_firing_history`, not `grafana_api_get`.
- Return the identifier the human would type into a ticket, plus the display name. Never only one.
- Add a `simulate_*` tool wherever a change is being considered (what would this threshold have fired on last month?). Simulation is reading.
- Include a `sources` field in every response: which backend, which timestamp. The verifier uses it.

## Credentials ladder

1. **Viewer** for inventory, audits, parity checks, briefs and cost sweeps. This covers most of the value.
2. **Propose** for workflows that open pull requests or draft tickets. Still no production write.
3. **Apply, gated** for the few workflows allowed to change production. Every apply goes through a diff a second agent approves and a human signs if the change is irreversible.

Move a workflow up the ladder only after it has run clean at the level below for a few weeks.
