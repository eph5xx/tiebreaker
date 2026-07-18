# Proposing factors, weights, and rubrics

Read at Step 2 (Propose). This file governs everything the confirm checkpoint
shows: which factors make the matrix, how they are weighted, and what the
1/3/5 anchors say. Keep anything the user supplied verbatim; propose only the
gaps.

## Factors

Propose 3-7 factors; aim for 4-6. Below 3 the matrix adds nothing over a gut
call; past 7 the weights dilute each other and the matrix stops
discriminating.

Run every candidate factor through five tests before presenting:

- **Distinct** - no two factors track the same underlying attribute. Overlap
  double-counts it silently ("Price" and "Value for money" are one factor).
  Merge overlapping factors and keep the sharper name.
- **Discriminating** - drop a factor when every option would land on the same
  score; it adds cost, not signal. Exception: the user asked for it. Keep it
  and let the tie show in the table.
- **Covering** - every concern the user voiced maps to some factor, and any
  factor they never hinted at earns its place through its rationale.
- **Assessable** - scoreable from findable evidence (web mode) or from the
  user's stated preferences and general knowledge (reasoning mode).
- **Direction-normalized** - 5 is always the better end for the decider.
  Invert cost-like factors: for Price, 5 = cheapest.

## Weights (1-5)

| Weight | Meaning |
|--------|---------|
| 5 | Decisive - could overturn everything else combined. At most two factors get it. |
| 4 | Major driver of the decision. |
| 3 | Matters. |
| 2 | Minor. |
| 1 | Tiebreaker only. |

Spread the weights: when everything sits at 4-5, nothing is weighted. Weights
are proposed before any scoring happens and frozen at the confirm checkpoint -
criteria first, options second, so no weight can drift to advocate for an
option somebody already prefers.

Each weight carries a one-line rationale saying why this weight for this
decider. Quote the user's own words when they gave any: "you said the budget
is hard-capped" justifies Price at 5.

## Anchored rubrics

Each factor gets three anchors spelling out what 1, 3, and 5 concretely mean
for this decision. The anchors are the only calibration scoring agents share,
so they carry the whole burden of cross-agent comparability. Rules:

- **Anchor 1/3/5 only.** 2 and 4 are interpolations, never anchored.
- **Concrete and observable** - numbers, thresholds, named capabilities,
  behavioral descriptions. An anchor a stranger could apply and land on the
  same score. "Poor / okay / great" is not an anchor.
  - Price: 5 = under $1,400; 3 = around $2,000; 1 = over $2,600.
  - Battery: 5 = 12h+ real-world; 3 = about 8h; 1 = under 5h.
- **Calibrated to this option set** - anchor against the realistic range of
  the options on the table so scores spread. If every option would hit 5, the
  anchors are miscalibrated: tighten them.
- **Monotone** - 5 strictly better than 3, 3 strictly better than 1.
- **One line per anchor.**
- **Subjective factors get behavioral proxies** - still observable, even when
  personal. Team fit: 5 = you would pick these coworkers again; 1 = you dread
  the standup.

## Terminology note

The classic Pugh matrix scores options +/S/- against a datum option;
Tiebreaker builds the weighted-decision-matrix generalization commonly called
Pugh-style.

Method grounding:
[ASQ on decision matrices](https://asq.org/quality-resources/decision-matrix),
[airfocus on weighted decision matrices](https://airfocus.com/blog/weighted-decision-matrix-prioritization/),
[behaviorally anchored rating scales](https://peoplemanagingpeople.com/performance-management/behaviorally-anchored-rating-scale/).
