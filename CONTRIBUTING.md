# Contributing

Thanks for helping make Tiebreaker better.

## Repo layout

The skill itself lives in `skills/tiebreaker/` - that folder is what installers
(`npx skills add eph5xx/tiebreaker`) copy and what a manual symlink points at.
Everything at the repo root is documentation and repo meta, never installed.

- `skills/tiebreaker/SKILL.md` - the orchestrator: input parsing, run rules, the five steps
- `skills/tiebreaker/references/` - step-specific material, loaded just in time:
  - `weights.md` - factor, weight, and anchored-rubric authoring (Step 2)
  - `scoring.md` - scoring-agent prompt template, output grammar, validation (Step 4)
  - `strategy.md` - the four scoring strategies, batching, research budgets
  - `output.md` - verdict layout (Step 5)
  - `log.md` - reasoning-log file format and path (Step 5, unless `--no-log`)
- `examples/` - worked end-to-end decisions, kept in sync with the formats above

## Dev loop

1. Symlink the skill folder: `ln -s "$PWD/skills/tiebreaker" ~/.claude/skills/tiebreaker`
2. Edit files.
3. Evaluate in a fresh Claude Code session each time. Leftover authoring context
   in an old session masks instruction gaps; a fresh session is the only honest
   test.

## Design invariants

Changes must preserve these. A PR that breaks one needs to argue for it
explicitly.

- Exactly one approval checkpoint (Step 3). The user is asked once.
- `SKILL.md` stays at 200 lines or fewer; detail lives in `references/`.
- The verdict prints in chat. The only file written is the reasoning log at
  `./.tiebreaker/<date>-<slug>.md`, and only when logging is on (the default;
  `--no-log` suppresses it). No CSV, HTML, or Sheets export.
- No `context: fork` in the frontmatter. A forked skill cannot stop and wait
  for the user at the confirm checkpoint.
- No `allowed-tools` self-grants. The user's permission settings govern.
- Cells and weights are integers 1-5.
- Scoring agents never see factor weights, only rubrics. Weights are applied by
  the orchestrator at verdict time.
- Every example matches SKILL.md Step 3 (proposal format) and
  `references/output.md` (verdict format) exactly.

## Pre-PR check: the four-strategy comparison

Run all four strategies on one fixed decision and compare. Fixed input removes
proposal variance: paste the decision plus the full factor block (factors,
weights, anchors) from `examples/laptop-column-web.md` into the invocation,
then reply "go" untouched at the checkpoint.

For each strategy in column, row, cell, table:

1. Fresh session.
2. `/tiebreaker <decision + factor block> --strategy=<s>`
3. Reply "go" at the checkpoint.
4. Record: wall-clock from "go" to verdict, agent count, retries or malformed
   output, the full matrix, the winner.

Report in the PR description (results are never committed):

|                      | column | row | cell | table |
|----------------------|--------|-----|------|-------|
| Agents               |        |     |      |       |
| Time go to verdict   |        |     |      |       |
| Retries / malformed  |        |     |      |       |
| Winner               |        |     |      |       |
| Max per-cell spread  |        |     |      |       |

Per-cell spread = max minus min of a cell's score across the four runs. Flag
any cell with spread of 2 or more: that is calibration instability, usually an
anchor problem in the rubric rather than a scoring bug.
