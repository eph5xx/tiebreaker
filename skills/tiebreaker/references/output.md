# The verdict

Read at Step 5. This is the only sanctioned output shape. Print it in chat;
write no files - no CSV, no HTML, no exports (deferred).

## Layout

```
## Verdict

| Option | Performance | Price | Battery | Repairability | Portability | Total |
|---|--:|--:|--:|--:|--:|--:|
| | ×5 | ×4 | ×4 | ×4 | ×3 | /100 |
| 🥇 **MacBook Pro 14** | 5 | 2 | 5 | 1 | 4 | **69** |
| 🥈 Framework 16 | 3 | 3 | 2 | 5 | 2 | 61 |
| 🥉 ThinkPad X1 Carbon | 2 | 3 | 3 | 2 | 5 | 57 |

**Winner: MacBook Pro 14** - <2-4 sentences>
```

Rules:

- Column headers are the bare factor names; the last header reads `Total`.
- The first body row is the weight row: `×<weight>` under each factor and
  `/<max>` under Total, where max = 5 x (sum of weights), calibrating the
  reader on what a perfect score would be.
- No Rank column - rows sorted by total, descending, imply the ranking. The
  top three option names carry a medal prefix (🥇 🥈 🥉); options ranked 4+
  are plain names. Tied options keep one row each, adjacent.
- Cells hold the raw 1-5 scores, never weighted products, so any reader can
  recompute a row by hand: total = sum(weight x score).
- The winner's name and total are bolded.
- Recompute every total from the validated scores immediately before
  printing. A total in chat must survive a hand check.

## The winner line

`**Winner: <name>**` followed by 2-4 sentences: where it won (name the
top-weighted factors that drove the total), where the runner-up beat it, and
why it still wins on the weighted sum. No hedging - the matrix made the call.

## Near-tie

When the gap between 1st and 2nd is at most the largest factor weight, add
one prose paragraph after the winner line: the gap in points; the observation
that a single 1-point scoring disagreement on <the heaviest factor> (w<N>)
would erase it; and the factor where the top two differ most - that is what
the call actually comes down to. No sensitivity table (deferred).

## Low-confidence footnote

When any cell was scored at low confidence, retried, or self-scored by the
orchestrator, close with one line:

```
Low confidence: <Option>/<Factor> (<one-phrase reason>), ...
```

Omit the line entirely when every cell came back clean at med or high
confidence.
