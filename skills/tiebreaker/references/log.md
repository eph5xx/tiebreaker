# The reasoning log

Read at Step 5 when logging is on (the default; skipped under `--no-log`). This
is the full-record file written to disk alongside the chat verdict. The chat
still gets the ranked table from [output.md](output.md); this file is the
durable, auditable record of how the matrix was scored.

## Where it goes

Write to `./.tiebreaker/<date>-<slug>.md`, relative to the current working
directory:

- `<date>` is today, `YYYY-MM-DD`.
- `<slug>` is a short kebab-case of the decision (e.g. `laptop-for-dev-work`),
  a handful of words at most.
- Create `./.tiebreaker/` if it does not exist; reuse it if it does.
- On a same-day slug collision, suffix `-2`, `-3`, ...

After writing, end the chat verdict message with exactly one line:

```
Details in .tiebreaker/<date>-<slug>.md
```

## Layout

Sections in this order:

```
# Tiebreaker: <decision one-liner>

<date> · <strategy> scoring · <N> agents · web research on|off

## Options

- <Option A>
- <Option B>
- <Option C>

## Factors

| Factor | Weight | Why | 1 = | 3 = | 5 = |
|---|--:|---|---|---|---|
| <name> | <w> | <one-line rationale> | <anchor> | <anchor> | <anchor> |

## Scores & reasoning

| Factor | Option | Score | Conf | Reasoning |
|---|---|--:|---|---|
| <factor> | <option> | <1-5> | <high\|med\|low> | <the agent's 1-2 sentences> |

## Verdict

<the exact ranked table from output.md>

**Winner: <name>** - <2-4 sentences>

<near-tie paragraph, when the gap warrants it>

<low-confidence footnote, when any cell earned one>

## Sources

1. <url> — <short label>
2. <url> — <short label>
```

Rules:

- The meta line mirrors the confirm-checkpoint plan line: strategy, agent
  count, research mode.
- **Factors** reproduces the confirmed spec verbatim - the same weights,
  rationales, and 1/3/5 anchors the user approved. Weights DO appear here (this
  is the record), unlike the scoring-agent prompts.
- **Scores & reasoning** is one row per (factor, option) cell. Reasoning is the
  1-2 sentence rationale the scoring agent returned, verbatim. Sort by factor,
  then by the option order in the verdict.
- **Verdict** is a copy of what printed in chat: the same ranked table (weight
  row, medals, bolded winner), the winner line, and - only when they apply -
  the near-tie paragraph and the low-confidence footnote.
- **Sources** appears only in web mode and only when URLs were used. List the
  deduped set of `SOURCE|` URLs collected across all scoring agents, not tied
  to specific cells. Omit the whole section in none/mcp modes or when no URLs
  came back.

The log is self-contained: someone who never saw the chat can read this file
and understand the decision, the rubric, every score, and why.
