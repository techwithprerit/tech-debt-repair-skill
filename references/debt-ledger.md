# The debt ledger: naming it, measuring it, showing it shrink

A repair programme dies when nobody can say what the debt is or whether it got smaller. This file gives the shared definition and the two instruments that keep the programme visible: a ledger of piles, and a scale scorecard per repair.

## One definition for the room

Tech debt is the future cost of a shortcut taken to ship today. The metaphor is a loan (Ward Cunningham, 1992), and the loan is not the mistake; never repaying it is.

| Term | Meaning | Infrastructure example |
|---|---|---|
| Principal | The shortcut itself: the work skipped to ship | A config edited by hand, an alert copied from another team, a migration left half done |
| Interest | What every later change costs because of it | Extra hours on each deploy, upgrade and incident while someone works around the shortcut |
| Bankruptcy | Interest consumes the whole week | The team only keeps things running; nothing is repaid, nothing new is built, nobody dares touch it |

## Where it hides

Code and architecture get the conference talks. This skill is about the other three:

- **Infrastructure**: hosts, clusters, pipelines, registries and DNS records that exist because nobody decided they should stop existing.
- **Operations**: alerts nobody trusts, dashboards that disagree, runbooks last opened during the previous outage, manual handoffs between tools.
- **Knowledge**: the map of what talks to what, held in the heads of people who have moved on.

A pile usually sits in more than one. Name all that apply; the knowledge part is what makes the other two expensive.

## Measuring interest before you repair

Principal is hard to price. Interest is observable, so measure that. For each candidate pile, have the agent read (read-only) and record:

- **Hours per week** spent working around it: tickets, on-call notes, deploy logs that mention it.
- **Pages and incidents** in the last 90 days that trace back to it.
- **Blocked or slowed changes**: upgrades postponed, migrations deferred, pull requests that needed a specialist only because of this pile.
- **Carrying cost**: the monthly bill for resources that exist only because of it.
- **Bus factor**: how many people can change it safely. One is a finding in itself.

Record the source for every figure. A number without a source is an estimate, and the ledger says so in the confidence column.

## The ledger

One row per pile, kept in git next to the context layer. Template in `templates/debt-ledger.md`.

Rules:

- Rows are added by anyone, and by the daily loop when it finds something unowned.
- A row is never deleted. It moves to `retired` with the date and the proof.
- The weekly loop re-reads the interest columns, so the ledger shows drift without anyone updating it by hand.
- Pick next week's pile from the ledger with `pile-picker.md`. High interest and cheap proof go first.

## Showing debt removed at scale

Leadership asks one question: is it working. Answer with a ratio, not an adjective. For every repair, count three things and the clock:

| Column | What to count | Who did it |
|---|---|---|
| Read | Every object the agents opened: repositories, aliases, instances, rules, dashboards, pipelines | Agents |
| Decided | Every decision a person made: keep, fix, retire, go or no-go | Humans |
| Retired | What no longer exists or no longer costs: resources at zero, rules removed, folders merged, incidents avoided | The team |
| Clock | Elapsed days from first read to proof | |

The read-to-decided ratio is the argument for the whole approach. Reading everything is what removes sampling, and sampling is where debt hides. The humans still made every decision; there were simply far fewer of them to make than objects to read.

Honest counting rules:

- Count objects from the source system, not from memory. Keep the query that produced each count.
- If a headline number is a sum of listed counts, say "about" and show the parts.
- A saving is "our estimate" until the bill moves, and the finance figure wins when the two disagree.
- A repair with no retired column is an audit, not a repair. Say so.
- Leave unfinished repairs out of the totals until their numbers are real.
- Never attribute a count to a named person.

Template in `templates/scorecard.md`.
