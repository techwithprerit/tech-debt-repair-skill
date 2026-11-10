# Field notes

Lessons from running this loop on the data platform of a large consumer marketplace (databases, caches, Kafka and a message bus across more than one region, run by a small team). Estate sizes, counts and savings figures are deliberately left out of this file; the talk carries the figures, this file carries the lessons.

## What the shape looked like

- Inventory: hundreds of repositories correlated to namespaces, schemas, caches and topics in one knowledge bundle, where earlier attempts by hand had stayed incomplete after weeks.
- Parity: every database cluster in scope checked read-only before the night; the blockers that surfaced were unresolved DNS aliases and a few instances at full storage, not the databases themselves.
- Wave plan: several versions in a week, each audited by a verifier that caught a replica host split across waves.
- The cutover night: a few hours, a handful of waves, a small database crew. Agents never touched production during the window.
- Observability: every dashboard and alert rule got a decision, applied in days, each reviewed with the specialist for that technology.
- Cost sweep after the migration: allocated-but-unused storage, legacy machine types at low CPU, caches at low memory, and an unowned backup vault. Quote the saving as the team's own estimate, never as a finance figure.

## The blunder

Everything that went well was reading: inventory, parity, planning, verification. The one production incident came from the one automated write with no verifier in front of it. An automated configuration change pointed a service at a cache whose name differed from the right one by a single token. Syntactically perfect, wrong. Three fixes, which are the three rules applied:

1. Writes go through a diff a second agent must approve.
2. Names that look alike get an explicit mapping table; never fuzzy-match identifiers.
3. The rollback decision tooling knows every resource's real owner.

Tell this story blamelessly and early. It is the most persuasive slide in the programme.

## The gate that was too soft

Version one of the wave plan contained decommissioned services. One inventory column carried a literal "guess" suffix on a few rows, and database endpoint aliases had been read as service names. The fix was a harder gate, not a smarter agent: the service catalogue is authoritative, everything else is a cross-check, and no catalogue access means no run.

## Things that surprised people

- Reviewing templates instead of pull requests cut human review time more than any model improvement.
- The verifier's known-regression list became the most valuable file in the repository; it grows by one line every time something is caught.
- A context layer of plain files beat every "knowledge graph" proposal because it diffs in a pull request.
- The briefs changed the team's day more than the repairs did; the repairs only happened because the briefs freed the week.
- Read-only access was easy to get approved once it was framed like a new hire's first-week permissions: scoped service account, audit log, revocable.

## Questions you will be asked

- *How did you get MCP access approved?* Scoped service accounts with viewer roles and an audit log, the same way as for a new engineer.
- *What did the agents cost?* Have the number ready; route execution to the cheapest capable model and the planning and verification to the strong one.
- *What about on-call?* Medium and low severity alerts go to an agent that investigates with read access and escalates to a human only when needed. It plugs into the same context layer.
- *Isn't this just more tooling?* The kit removes reasons the debt was unpaid; a product adds a reason. The test is whether the pile shrank this week.
