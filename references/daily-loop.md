# The daily loop: briefs and weekly reports

A repaired pile stays repaired only if something reads it every day. The brief is the cheapest form of that read, and it is the same agents, the same read-only tools and the same context layer as the repairs, run on a schedule.

## Rules for a brief people keep reading

1. **One page, same time every day.** Stagger several briefs by ten minutes so one human can read them in sequence.
2. **Every alert gets a verdict.** Real, false positive or known pattern, with the evidence (the query, the window, the number). No verdict, no line.
3. **Every abnormal resource gets a reason.** "Replica lag 40 s because of the nightly export" is a line; "replica lag 40 s" is not.
4. **Batch patterns are listed, never flagged.** An anomaly that recurs at the same time daily is a job. Keep a known-patterns list the brief maintains and the human can edit.
5. **Cost is read daily.** Per-resource trend and a month-end forecast. Weekly views use a seven-day window that ends a few days before today so billing lag never lies.
6. **One click to a ticket.** Every finding carries a pre-filled ticket that follows the team's ticket standard. The human decides; the agent files.
7. **Every run is archived.** Yesterday's brief is always one click away, and the week's briefs feed the weekly reports.
8. **Nothing in the loop can write.** Viewer credentials only. If a brief ever needs a write, that is a workflow, not a brief.
9. **Blur before you share.** When a brief leaves the team (a talk, a wiki page), identifiers and currency amounts are redacted.

## Brief template

```
# <Estate> brief · <date> · <time zone>
Status strip: <n> resources · <n> alerts in 24h (<n> real, <n> false, <n> batch) · cost MTD <trend> · forecast <value>

## Alerts, last 24 hours
| time | alert | resource | verdict | evidence | action |

## Abnormal resources
| resource | signal | value vs 7-day peak | reason | action |

## Cost
Per-resource movers (top 5 up, top 5 down) · month-end forecast · anything new since yesterday

## Housekeeping
Backups verified · certificates/versions nearing end of life · tickets opened from yesterday's brief and their status

## Known patterns (listed, not flagged)
```

## Weekly reports built from the daily reads

| Report | Day | Window | Reads |
|---|---|---|---|
| Cost trend | Friday | 7 days ending Wednesday | billing by resource, utilisation peaks |
| Alert noise | Monday | 7 days | firing history per rule; proposes retunes |
| Backups and retention | Monday | 7 days | backup success, restore tests, retention labels |
| Debt scorecard | monthly | 30 days | the repair scorecard plus piles picked and finished |

## Schedule hygiene

- A scheduled run that cannot reach a source of truth posts a one-line gate failure, not a brief with gaps.
- Keep the brief's prompt in version control next to the context layer; changes go through a pull request like any other code.
- Review the known-patterns list monthly; patterns that stopped recurring are removed so a real anomaly is not hidden behind an old entry.

## What to expect

A small team running a large multi-region estate of databases, caches and message systems went from dozens of browser tabs every morning to a few one-page briefs read over coffee. The repairs in the rest of this skill only became possible because the daily operating load stopped eating the week.
