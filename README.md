# tech-debt-repair

A Claude skill from the talk *AI at the Door: Turning Tech Debt Panic into Tech Debt Repairs* (Prerit Munjal, @techwithprerit).

It gives Claude an operating procedure for working on legacy infrastructure with AI agents: pick one pile, read-only hands, gate the inputs, propose then verify, loop it weekly. Agents read, correlate and check; humans decide and make the one irreversible click.

## Install

- **Claude Code**: `git clone https://github.com/techwithprerit/tech-debt-repair-skill ~/.claude/skills/tech-debt-repair` (personal), or clone it into `.claude/skills/tech-debt-repair/` inside a repository to share it with a team. Claude loads it when a task matches the description.
- **Claude.ai / Cowork**: download this repository as a ZIP (Code → Download ZIP), rename the folder inside to `tech-debt-repair`, and upload it as a skill in Settings → Capabilities (or your organisation's skills page).

## Layout

```
tech-debt-repair/
├── SKILL.md                     # the method; works on its own
├── references/
│   ├── debt-ledger.md           # what debt is, measuring interest, showing it shrink
│   ├── pile-picker.md           # choosing the first repair
│   ├── estate-map.md            # the map, units of change, ways back, waves and pilots
│   ├── mcp-readonly.md          # read-only MCP server checklist and tool set
│   ├── agent-roles.md           # planner / specialists / verifier, model routing, briefs
│   ├── verification.md          # proof catalogue, retire-never-delete, verdict format
│   ├── repair-playbooks.md      # migration parity, observability, CI conversion, repo cleanup, cost sweep
│   ├── daily-loop.md            # morning brief rules, weekly reports
│   ├── context-layer.md         # the plain-files estate map
│   └── field-notes.md           # numbers, the blunder, questions you will be asked
└── templates/
    ├── debt-ledger.md
    ├── estate-map.md
    ├── repair-plan.md
    ├── verifier-brief.md
    ├── morning-brief.md
    └── scorecard.md
```

## Try it

Ask Claude, with the skill installed:

- "We have 300 Jenkins pipelines nobody dares touch. Where do we start?"
- "Design a read-only MCP server for our Grafana and service catalogue."
- "Write the verifier brief for our region migration wave plan."
- "Set up a daily brief for our Cloud SQL estate."
- "Map our estate before we move regions, with the unit of change and the way back for each engine."
- "Build a debt ledger for the platform team and show what we retired this quarter."

## What's new

See `CHANGELOG.md`. Version 1.1 adds the debt ledger, the estate map with units of change, pilots, and a scale scorecard for showing what was retired.

Views in the talk and in this skill are the author's own and do not represent his employer. The skill contains no employer-internal identifiers, system names, figures or data; every example is generic or anonymised.
