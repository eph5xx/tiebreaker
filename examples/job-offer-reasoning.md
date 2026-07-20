# Example: job offer vs startup vs master's (row-wise, no web)

> Illustrative transcript. Employers, numbers, and scores were invented at
> time of writing to demonstrate the format - not advice.

The subjective path: the skill auto-invokes from plain conversation (no slash
command), strategy and research mode are picked in natural language, and the
verdict lands in near-tie territory.

## Invocation

```
Help me decide properly: stay at BigCo as a senior engineer, join Nimbus as
founding engineer, or take the ML master's I got into. Don't search the web -
you already know everything relevant. Score it row-wise.
```

"Help me decide" triggers the skill. "Score it row-wise" selects the row
strategy; "don't search the web" selects reasoning mode.

## Proposal (the confirm checkpoint)

**Decision:** Choose the next 2-4 years: stay senior at BigCo, found-engineer
at Nimbus, or do the ML master's.
**Options:** BigCo - Nimbus - ML master's
**Plan:** row scoring - 3 agents - web research off

| Factor (w) | Why | 1 = | 3 = | 5 = |
|---|---|---|---|---|
| Growth (5) | You said this decade sets your ceiling - compounding skills is the decisive axis | repeating your current year | steady incremental growth | new-role skills that compound for a decade |
| Compensation (4) | Fixed mortgage; you said a cut is survivable but not a deep one | deep cut, years to recover | roughly flat, or modest cut with clear upside | clear rise over today |
| Mission pull (4) | You keep returning to what the work is for | indifferent to the problem | interesting enough to show up | you would work on it unpaid on weekends |
| Day-to-day energy (4) | The last two years drained you; you said that cannot continue | drained by lunch most days | mixed weeks | most days end with energy left |
| Stability (3) | Real constraint, but you called it manageable | role could vanish in 6 months | safe for about 18 months | survives a bad year easily |

> Edit anything - factors, weights, anchors, strategy, research - or say
> **go** to score.

## User reply

```
go
```

## Scoring

Row-wise: 3 agents launched in a single message, one option each, every
prompt carrying that option's context and all five rubrics. Zero web calls -
each agent scored its option factor by factor against the anchors, from the
decider's stated situation only. 15 cells valid on the first pass; one
flagged low confidence (the master's stipend was never stated).

## Verdict

| Option | Growth | Compensation | Mission pull | Day-to-day energy | Stability | Total |
|---|--:|--:|--:|--:|--:|--:|
| | ×5 | ×4 | ×4 | ×4 | ×3 | /100 |
| 🥇 **Nimbus (founding engineer)** | 5 | 2 | 5 | 4 | 1 | **72** |
| 🥈 ML master's | 5 | 1 | 3 | 4 | 4 | 69 |
| 🥉 BigCo (stay senior) | 2 | 5 | 2 | 2 | 5 | 61 |

**Winner: Nimbus (founding engineer)** - It maxes the decisive factor
(Growth, w5) and Mission pull, the thing you kept circling back to. The
master's matches it on growth and beats it clearly on stability; Nimbus still
edges ahead on mission and pay. BigCo pays best and is safest, but repeating
a draining year fails the two axes you weighted at 5 and 4.

Near-tie: 3 points apart - a single 1-point scoring disagreement on Growth
(w5) would erase the gap. The top two differ most on Stability (master's 4
vs Nimbus 1) against Mission pull (Nimbus 5 vs master's 3): the call comes
down to how much runway risk you can absorb for work that genuinely pulls
you.

Low confidence: ML master's/Compensation (stipend never stated).

Details in .tiebreaker/2026-07-20-next-move.md

## What this demonstrates

- Model invocation: plain "help me decide" conversation, no slash command.
- Natural-language configuration: "score it row-wise" and "don't search the
  web" replace `--strategy=row --no-web`.
- Behavioral anchors make subjective factors scoreable ("you would work on it
  unpaid on weekends").
- The near-tie paragraph appears because the gap (3) is at most the largest
  weight (5), and it names the factor the decision actually hinges on.
- Reasoning-mode evidence gaps surface in the low-confidence footnote instead
  of silently hardening into scores.
- The reasoning log is still written (default), captured per
  [references/log.md](../skills/tiebreaker/references/log.md); with `--no-web`
  it has no Sources section.
