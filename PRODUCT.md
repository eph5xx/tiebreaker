# Tiebreaker — Product

## Problem
People get stuck between a handful of options and spin. The reasoning is real but
unstructured, so the decision never resolves. Tiebreaker turns that into a
weighted decision matrix and hands back a ranked verdict you can trust — fast,
with one point of human control.

## What it is
A single Claude Code skill, `/tiebreaker`. It builds a Pugh-style weighted scoring
matrix:

- **rows** = your options
- **columns** = the factors that matter
- **cells** = a 1–5 score
- **weights** = how much each factor matters

`score(option) = Σ (weight_factor × cell_1to5)` — rank by total, highest wins.

Speed is a first-class goal: one confirmation checkpoint, parallel scoring by
default, and an option to skip web research for subjective calls.

## How it works
1. **Intake** — you bring the decision + options (and factors if you have them).
2. **Propose** — Claude fills the gaps in one shot: the factors, a weight per
   factor (1–5 + one-line rationale), and a **custom anchored rubric per factor**
   spelling out what 1 / 3 / 5 concretely mean for *this* decision
   (e.g. Price: 5 = <$500, 3 = ~$1k, 1 = >$2k).
3. **Confirm** — one checkpoint: you edit or approve the factors, weights, and
   rubrics.
4. **Score** — scoring agents fill the matrix (see strategies below).
5. **Verdict** — a ranked markdown table + the winner + a short rationale.

## Design decisions
- **One-shot + confirm**, not a step-by-step interview. Fewer round-trips, single
  point of control.
- **Anchored rubrics per factor.** Scores are reproducible and auditable, not a
  vague "5 = good."
- **Claude proposes weights, you edit.** Balances speed with control.
- **Configurable scoring fanout** (the speed/quality/cost lever):
  - **column-wise (default)** — one agent per factor, consistent rubric down the
    column, parallelism = #factors
  - **row-wise**, **cell-wise**, **table-wise** also supported
  - selectable by explicit arg (`/tiebreaker --strategy=cell`) **or** natural
    language ("score cell-wise")
- **Research:** WebSearch/WebFetch + your context by default; MCP on request; a
  pure-reasoning path for subjective decisions where the web adds nothing.

## Scope

### MVP
- The full one-shot + confirm flow.
- All four scoring strategies, default column-wise.
- Verdict printed in chat: ranked matrix table + winner + short rationale.

### Deferred (post-MVP)
- Sensitivity / tie analysis (which weight or cell flips the result).
- Per-cell evidence notes; explicit runner-up vs winner tradeoff.
- CSV / HTML image / Google Sheets output.

## Definition of done (OSS-ready)
- [ ] `SKILL.md` — one-shot + confirm flow, strategy arg + NL, default column-wise
- [ ] `references/` — `strategy.md`, `scoring.md`, `weights.md`, `output.md`
- [ ] 1–2 worked examples (`examples/`, input → matrix → verdict)
- [ ] `README.md` with install + invoke + strategy-config docs
- [ ] `LICENSE` + `CONTRIBUTING.md`
- [ ] Manual dev check: run all four strategies on a sample decision to compare
      speed/quality (dev-time, not a shipped harness)
