---
name: tiebreaker
description: Settle a decision between concrete options with a weighted scoring
  matrix (Pugh-style). Proposes factors, 1-5 weights, and anchored 1/3/5
  rubrics, confirms once, scores every option with parallel research subagents,
  and prints a ranked verdict table with a winner. Use when the user is stuck
  choosing between named options - "help me decide", "X vs Y vs Z", "which
  should I pick/buy/use" - or asks for a decision matrix, weighted scoring, or
  a tiebreaker.
argument-hint: "[decision + options] [--strategy=column|row|cell|table] [--no-web] [--no-log]"
---

# Tiebreaker

Settle the decision with a weighted matrix: rows = options, columns = factors,
cells = 1-5 scores, each factor weighted 1-5. score(option) = sum of
(weight x cell). Highest total wins.

## Input

$ARGUMENTS

Pin five fields from the input above (or from the conversation when invoked
without arguments):

| Field | How |
|-------|-----|
| Strategy | `--strategy=` flag beats natural language beats the default: column. Aliases: col, factor, per-factor, column-wise = column; option, per-option, row-wise = row; per-cell, cell-wise = cell; matrix, whole table, one agent, all at once = table. |
| Research | web (default); none when `--no-web` or the user says don't search / just reason / it's subjective; mcp only when the user names an MCP server or tool to use. |
| Decision | One sentence: what is being decided, for whom, with what constraints. |
| Options | The named options. At least 2. |
| Given spec | Any factors, weights, or rubric anchors the user already supplied - kept verbatim, propose only the gaps. |
| Log | on (default): write the reasoning log at Step 5; off when `--no-log` or the user says don't save / no file. |

## Rules for the whole run

- Exactly ONE approval checkpoint (Step 3). After it, never ask for approval
  again.
- The verdict prints in chat. Unless `--no-log`, also write a full-record
  reasoning log to `./.tiebreaker/<date>-<slug>.md` at Step 5 and end the
  verdict message with `Details in <path>`. CSV/HTML/Sheets export stays
  deferred.
- Cells and weights are integers 1-5. Weights are frozen at the checkpoint.
- You compute all weighted totals yourself and re-check the arithmetic before
  printing.
- Scoring agents never see factor weights - rubrics only. You apply weights
  at verdict time.

## Step 1 - Intake

Pin the five fields. If the decision or a second option is missing, ask one
short question for just the missing pieces - this is intake, not the
checkpoint.

Done when: decision, 2+ options, strategy, and research mode are each pinned.

## Step 2 - Propose

Read [references/weights.md](references/weights.md) now. Then draft in one
shot:

- 3-7 factors (aim 4-6), each passing the five factor tests
- a weight 1-5 per factor with a one-line rationale
- a 1/3/5 anchored rubric per factor, concrete and specific to THIS decision

Keep any user-supplied factors, weights, or anchors verbatim.

Done when: every factor has a weight, a rationale, and three anchors, and no
two factors double-count the same attribute.

## Step 3 - Confirm (the single checkpoint)

Present compactly:

```
**Decision:** <one line>
**Options:** A - B - C
**Plan:** <strategy> scoring - <N> agents - web research on|off

| Factor (w) | Why | 1 = | 3 = | 5 = |
|---|---|---|---|---|
```

N = agents the strategy implies (column: one per factor; row: one per option;
cell: one per cell; table: 1). If N > 25, add one line: "<strategy> means N
agents here; column-wise needs <F> for similar quality - keeping your choice
unless you say otherwise." If N > 50, use column-wise instead and say so in
the plan line.

End the message with exactly:

> Edit anything - factors, weights, anchors, strategy, research - or say
> **go** to score.

STOP and wait for the reply. On reply: apply every edit without re-presenting
the proposal, answer any question briefly, and move to Step 4.

Done when: the user has replied and the matrix spec is final.

## Step 4 - Score

Read [references/scoring.md](references/scoring.md) now - every run. Also
read [references/strategy.md](references/strategy.md) when the strategy is
not column or the matrix has more than 30 cells.

Fast path: research mode none + table strategy = score the matrix yourself
inline against the rubrics, capturing a 1-2 sentence reasoning per cell (so the
log has content even with no subagents), then go to Step 5.

Otherwise fan out:

- One subagent per unit: column = per factor, row = per option, cell = per
  cell, table = one agent for everything.
- Build each prompt from the template in scoring.md with every variable
  filled. Prompts are fully self-contained - subagents cannot see this
  conversation.
- Launch ALL agents in a SINGLE message - one Task call each, subagent type
  general-purpose - so they run in parallel. More than 10 agents: waves of
  10.
- Collect only output lines matching `SCORE|`; validate per scoring.md.
- Cells missing or malformed after all agents return: ONE retry agent for
  just those cells; anything still failing, score yourself at low
  confidence.

Done when: every (option, factor) cell holds a validated integer 1-5.

## Step 5 - Verdict

Read [references/output.md](references/output.md) now. Recompute every
weighted total from the validated scores, then print: the ranked matrix
table, the winner line with a 2-4 sentence rationale, the near-tie paragraph
when the top-two gap is at most the largest factor weight, and the
low-confidence footnote if any cell earned one.

Then, unless `--no-log`, read [references/log.md](references/log.md), write the
full-record reasoning log to `./.tiebreaker/<date>-<slug>.md`, and append one
final line to the chat message: `Details in <path>`. Skip both under
`--no-log`.

Done when: table, winner, and rationale are in chat, a hand check of the totals
holds up, and (unless `--no-log`) the log file is written and referenced.

## References

- [references/weights.md](references/weights.md) - factor tests, weight
  scale, anchor authoring. Read at Step 2.
- [references/scoring.md](references/scoring.md) - prompt template, output
  grammar, validation. Read at Step 4.
- [references/strategy.md](references/strategy.md) - strategy tradeoffs,
  batching, research budgets. Read at Step 4 for non-column strategies or
  matrices over 30 cells, or when the user asks about strategies.
- [references/output.md](references/output.md) - verdict layout. Read at
  Step 5.
- [references/log.md](references/log.md) - reasoning-log file format and path.
  Read at Step 5 unless `--no-log`.
