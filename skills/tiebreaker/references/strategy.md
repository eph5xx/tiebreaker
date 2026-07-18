# Scoring strategies

Read at Step 4 when the strategy is not column, when the matrix exceeds 30
cells, or when the user asks about strategy tradeoffs.

## The lever

Strategy picks the consistency unit: which slice of the matrix one agent sees
whole. Cells scored by the same agent are internally consistent for free;
cells split across agents stay comparable only through the anchored rubrics.
More agents buy parallel research depth and wall-clock speed at the price of
heavier reliance on the anchors and higher cost.

## The four strategies

For O options and F factors:

| Strategy | Agents | Consistency unit | Best when |
|----------|--------|------------------|-----------|
| column (default) | F | one factor, across all options | Almost always. Cross-option comparability per factor is exactly what a ranking needs. |
| row | O | one option, across all factors | Complex options that each need deep individual research: job offers, vendors, houses. |
| cell | O x F | none - anchors only | High stakes, small matrix, maximum per-cell research depth. Most expensive. |
| table | 1 | the whole matrix | Subjective or pure-reasoning decisions; cheap sanity runs. Perfect internal consistency, least parallelism. |

## Context building per strategy

What each agent shape needs in its prompt to score well. The corresponding
`{STRATEGY_RULE}` texts live in [scoring.md](scoring.md).

- **Column agent** - all options with their context, one factor block. Its
  power is comparison: it ranks the options on its factor before scoring, so
  scores land relative to the real spread.
- **Row agent** - one option with full context, all factor blocks. Its risk
  is halo: a strong overall option scoring high everywhere. The strategy rule
  pins each factor to its own anchors.
- **Cell agent** - one option, one factor block, plus the decision framing.
  It sees no sibling cells, so the anchor text is its entire calibration;
  anchors must be at their sharpest for cell-wise to work
  ([weights.md](weights.md)).
- **Table agent** - everything. Its risk is option-by-option drift, scoring
  each option holistically instead of comparing per factor; the strategy rule
  forces factor-by-factor traversal, keeping the column benefit inside a
  single agent.

## Agent counts and batching

- At most 10 subagents per message; larger fanouts go in waves of 10, next
  wave after the previous returns.
- At the confirm checkpoint, a strategy implying more than 25 agents gets one
  warning line with the column-wise count as the comparison; the user's
  choice stands unless they change it.
- Above 50 agents, cell-wise stops being defensible: the run switches to
  column-wise and the plan line says so. A matrix that size gains nothing
  from per-cell isolation.

## Research budgets

Per agent in web mode: at most 2 searches per assigned cell and 8 searches
total; the table agent gets 12. Reasoning mode: zero external calls,
regardless of strategy. Budgets ride into every prompt via
`{RESEARCH_MODE_BLOCK}` ([scoring.md](scoring.md)).

## The fast path

Reasoning mode plus table strategy: the orchestrator scores the matrix
itself, inline, no subagents. Fastest and cheapest run; right for subjective
decisions where the decider's own context is the entire evidence base.
