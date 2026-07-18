# Scoring agents: prompt template, output grammar, validation

Read at Step 4 (Score), every run. This file is how the orchestrator turns the
confirmed matrix spec into subagent prompts and how it validates what comes
back.

## Composing prompts

Every scoring agent's prompt is fully self-contained: subagents cannot see the
conversation and must not depend on reading skill files. Build each prompt
from the template below with every variable filled:

| Variable | Content |
|----------|---------|
| `{DECISION}` | One sentence: what is being decided, for whom, key constraints. |
| `{OPTIONS_BLOCK}` | The options this agent scores - column and table agents get all options; row and cell agents get only their own. Each option: its name plus any user-provided context about it, verbatim. |
| `{USER_CONTEXT}` | The decider's stated preferences and constraints, verbatim; "none" if empty. |
| `{RESEARCH_MODE_BLOCK}` | One of the three fixed texts below. |
| `{ASSIGNMENT}` | Explicit list of the (option, factor) cells this agent owns. |
| `{N_CELLS}` | Count of assigned cells. |
| `{FACTOR_BLOCKS}` | For each assigned factor: name and the three anchors, copied verbatim from the confirmed spec. Never include weights. |
| `{STRATEGY_RULE}` | The per-strategy consistency rule below. |

Research mode texts:

- web: "Use WebSearch/WebFetch for evidence. At most 2 searches per cell and 8
  per agent total (12 if you score the whole matrix). Prefer sources from the
  last 12 months; note stale data in the basis."
- none: "Do not use the web, MCP, or any other external tool. Reason from the
  context above and general knowledge only."
- mcp: "Use <the user-named MCP tools> for evidence, at most 8 calls total;
  WebSearch as fallback."

Strategy rules:

- column: "You score one factor across all options. Compare the full option
  set on this factor first - rank them internally - then emit the lines."
- row: "You score one option across all factors. Score each factor
  independently against its own anchors; do not let an overall impression of
  the option bleed across factors."
- cell: "You score a single cell. You cannot see the other cells; the anchor
  text is the only calibration - apply it literally."
- table: "You score the whole matrix. Work factor by factor - complete one
  factor across all options before moving to the next - not option by option."

## The prompt template

```
You are a scoring agent filling part of a weighted decision matrix. You cannot
see the main conversation; everything you need is below. Return ONLY the
output lines specified at the end - no prose, no headers, no markdown.

DECISION: {DECISION}

OPTIONS:
{OPTIONS_BLOCK}

DECIDER CONTEXT: {USER_CONTEXT}

RESEARCH: {RESEARCH_MODE_BLOCK}

YOUR ASSIGNMENT - score exactly these {N_CELLS} cells:
{ASSIGNMENT}

{FACTOR_BLOCKS}

SCORING RULES
- Score against the rubric anchors literally. The anchors are the only
  calibration shared with agents scoring other cells; do not drift from them.
- 2 and 4 are interpolations: 4 = clearly past the 3 anchor but short of the
  5 bar; 2 = clearly below the 3 anchor but above the 1 floor.
- Integers 1-5 only. Evidence first; when evidence is missing after research,
  reason from what you know and mark confidence low. Score every assigned
  cell.
- Apply the rubric as written. If it fits an option badly, score it as
  written anyway and say so in the basis.
- {STRATEGY_RULE}

OUTPUT - one line per assigned cell, exactly:
SCORE|<option name exactly as written above>|<factor name exactly as written above>|<1-5>|<high|med|low>|<basis, max 25 words, no "|" characters>
Then one final line:
DONE|cells={N_CELLS}
```

Format `{FACTOR_BLOCKS}` as, per factor:

```
FACTOR: <name>
RUBRIC: 5 = <anchor>; 3 = <anchor>; 1 = <anchor>
```

## Validation

Collect each agent's output and validate before building the verdict:

1. Keep only lines matching `^SCORE\|`; stray prose is ignored by
   construction.
2. Split on `|`: exactly 6 fields.
3. Option and factor must match the confirmed spec character for character -
   the template demands a verbatim echo for exactly this reason.
4. Score must be an integer 1-5; confidence one of high, med, low.
5. Duplicate cell: first line wins.
6. Diff the collected cells against the expected option x factor set.

Gaps or malformed cells after all agents return: launch ONE retry agent
covering all missing cells at once, cell-style prompt ({ASSIGNMENT} lists the
missing cells; include every relevant factor block). Anything still missing
after the retry: score it yourself from available context and mark confidence
low.

Cells that were retried or self-scored surface in the verdict's
low-confidence footnote (references/output.md).

The one-line grammar doubles as context protection: a 35-cell matrix comes
back as 35 short lines, not 35 research reports. The regex filter makes any
agent that returns prose anyway harmless.
