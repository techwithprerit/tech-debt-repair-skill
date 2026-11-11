# Repair plan · <pile>

## The debt
<One paragraph. What it is, how long it has been there, what it costs each week. No names.>

## Scope rule
<One sentence a stranger could apply.>

## Sources of truth and gates
| Fact the run depends on | Authoritative source | Cross-check | If unreachable |
|---|---|---|---|
| | | | Stop |

## What the agents will read
<Systems, tools, credential (viewer), confirmation that nothing can write.>

## Proposed change
<Per object: current state → proposed action.>

## Proof of success (decided now)
<Diff / parity table / replay / burn-in. What the verifier will measure.>

## Verifier brief
<Separate file; the verifier does not read this plan's reasoning.>

## Human decisions
| Decision | Owner | When |
|---|---|---|
| Thresholds / scope | | before run |
| Go / no-go | | after verdict |
| The irreversible step | | at the step, by hand |

## Unit of change and way back
<What moves as one operation (a database, a whole cluster, a host, one record)? Time budget per operation. Way back: repeatable, one-shot or none. Who calls it (role)? Tested when?>

## Pilot
<What goes first, cut over for real, to prove the machinery. The bar it must clear, agreed before it runs.>

## Loop
<What gets scheduled afterwards, at what cadence, reading what.>

## How it will be counted
<Objects read, decisions made, things retired, days on the clock. Where each count comes from.>
