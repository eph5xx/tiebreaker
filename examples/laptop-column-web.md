# Example: which laptop (column-wise, web research)

> Illustrative transcript. Products, prices, and scores were invented at time
> of writing to demonstrate the format - not advice, not current market data.

The default path: no flags, column-wise scoring, web research on. Also shows
an edit at the checkpoint being applied without a second confirmation.

## Invocation

```
/tiebreaker which laptop for ML dev + travel: MacBook Pro 14 M4 Pro vs
ThinkPad X1 Carbon Gen 13 vs Framework 16
```

## Proposal (the confirm checkpoint)

**Decision:** Pick a laptop for local ML development and frequent travel.
**Options:** MacBook Pro 14 M4 Pro - ThinkPad X1 Carbon Gen 13 - Framework 16
**Plan:** column scoring - 5 agents - web research on

| Factor (w) | Why | 1 = | 3 = | 5 = |
|---|---|---|---|---|
| Performance (5) | ML dev is the stated job; local model capacity decides daily usability | 16GB RAM, cloud-only for real models | 32GB, 7B models run comfortably | 48GB+ unified/VRAM, 13B+ workable locally |
| Price (4) | Personal purchase; no budget given, so price stays a major driver | over $3,000 | around $2,400 | under $1,800 |
| Battery (4) | Travel is half the brief - unplugged hours are load-bearing | under 5h real-world | about 8h | 12h+ real-world |
| Portability (3) | Carried through airports weekly | over 2.4kg | about 1.8kg | under 1.4kg |
| Repairability (2) | Nice to have on a 4-year horizon; you did not raise it | soldered everything, authorized service only | battery and SSD user-replaceable | mainboard, RAM, battery replaceable with published manuals |

> Edit anything - factors, weights, anchors, strategy, research - or say
> **go** to score.

## User reply

```
Repairability matters more to me - make it 4. go
```

Repairability moves to weight 4. No second confirmation; scoring starts.

## Scoring

Column-wise: 5 agents launched in a single message, one factor each, every
prompt self-contained with the factor's rubric and all three options. Each
agent compared the three laptops on its factor (2 searches per cell budget),
then returned three `SCORE|` lines. 15 cells came back valid on the first
pass.

## Verdict

| Option | Performance | Price | Battery | Repairability | Portability | Total |
|---|--:|--:|--:|--:|--:|--:|
| | ×5 | ×4 | ×4 | ×4 | ×3 | /100 |
| 🥇 **MacBook Pro 14 M4 Pro** | 5 | 2 | 5 | 1 | 4 | **69** |
| 🥈 Framework 16 | 3 | 3 | 2 | 5 | 2 | 61 |
| 🥉 ThinkPad X1 Carbon Gen 13 | 2 | 3 | 3 | 2 | 5 | 57 |

**Winner: MacBook Pro 14 M4 Pro** - It took the two heaviest factors: 48GB
unified memory runs 13B models locally (Performance, w5) and real-world
battery clears 12 hours (Battery, w4). Framework 16 swept Repairability even
after your upweight to 4, but gave back too much on battery and weight. The
8-point gap means no single scoring call flips this.

## What this demonstrates

- Flag-free invocation lands on the defaults: column strategy, web research.
- One checkpoint: the weight edit was applied and scoring started
  immediately, with no re-presentation of the proposal.
- Anchors are concrete enough that every cell survives a "would a stranger
  score this the same way?" check.
- Totals are hand-checkable from the table: 5x5 + 2x4 + 5x4 + 1x4 + 4x3 = 69.
- Gap (8) above the largest weight (5), so no near-tie paragraph appears.
