# Tiebreaker

A Claude Code skill that settles hard decisions.

You are stuck between a handful of options and spinning. Tiebreaker turns the
reasoning you already have into a weighted decision matrix - rows are your
options, columns are the factors that matter, cells are 1-5 scores, factors
carry 1-5 weights - and hands back a ranked verdict you can audit:

```
score(option) = sum(weight x cell)  ->  highest total wins
```

| Option | Performance | Price | Battery | Repairability | Portability | Total |
|---|--:|--:|--:|--:|--:|--:|
| | ×5 | ×4 | ×4 | ×4 | ×3 | /100 |
| 🥇 **MacBook Pro 14 M4 Pro** | 5 | 2 | 5 | 1 | 4 | **69** |
| 🥈 Framework 16 | 3 | 3 | 2 | 5 | 2 | 61 |
| 🥉 ThinkPad X1 Carbon Gen 13 | 2 | 3 | 3 | 2 | 5 | 57 |

## How it works

1. **Intake** - you bring the decision and the options (and factors, if you
   already know them).
2. **Propose** - Claude fills the gaps in one shot: factors, a 1-5 weight per
   factor with a one-line rationale, and an anchored rubric per factor
   spelling out what 1/3/5 concretely mean for this decision
   (Price: 5 = under $1,400; 3 = around $2,000; 1 = over $2,600).
3. **Confirm** - one checkpoint. Edit anything or say go. You are never asked
   for approval again.
4. **Score** - parallel subagents fill the matrix, scoring against the
   anchors. They never see the weights, so no factor gets a halo.
5. **Verdict** - the ranked table, the winner, and a short rationale, printed
   in chat.

One checkpoint, parallel scoring, no files written. Speed is a feature.

## Install

With [skills](https://github.com/vercel-labs/skills), for the current project:

```sh
npx skills add eph5xx/tiebreaker
```

Personal (available in all projects):

```sh
npx skills add eph5xx/tiebreaker -g
```

Manually, without the CLI - the skill lives in `skills/tiebreaker/`:

```sh
git clone <repo-url> && ln -s "$PWD/tiebreaker/skills/tiebreaker" ~/.claude/skills/tiebreaker
```

The directory name is the command name - keep it `tiebreaker`. The skill
grants itself no tools (`allowed-tools` is deliberately absent), so depending
on your permission settings you may see prompts for web searches or subagent
launches; approve per your own policy.

## Use

```
/tiebreaker which standing desk: Jarvis vs Uplift vs Ikea Trotten
/tiebreaker Postgres vs SQLite for a single-server app --strategy=table --no-web
```

Or just talk - the skill triggers on decision language:

```
I'm stuck choosing between Hetzner and Fly.io for hosting. Help me decide properly.
```

| Config | Flag | Natural language |
|---|---|---|
| Scoring strategy | `--strategy=column\|row\|cell\|table` | "score it cell-wise", "one agent per option", "whole table at once" |
| Skip web research | `--no-web` | "don't search", "just reason", "it's subjective" |
| Use MCP tools | - | name them: "use the Notion MCP for evidence" |

## Scoring strategies

For O options and F factors:

| Strategy | Agents | Best when |
|---|---|---|
| column (default) | F | Almost always - each factor scored consistently across all options |
| row | O | Complex options that each need deep research (job offers, vendors) |
| cell | O x F | High stakes, small matrix, maximum per-cell depth |
| table | 1 | Subjective calls and cheap sanity runs |

Details and tradeoffs: [references/strategy.md](skills/tiebreaker/references/strategy.md).

## Research modes

- **web (default)** - scoring agents use WebSearch/WebFetch plus whatever
  context you gave.
- **none** - pure reasoning, zero external calls. Fastest; right for
  subjective decisions where the web adds nothing. Combined with the table
  strategy it runs with no subagents at all.
- **mcp** - on request only: name the MCP tools to use.

## Examples

Worked end-to-end transcripts:

- [Which laptop](examples/laptop-column-web.md) - default path: column-wise,
  web research, an edit at the checkpoint, a decisive verdict.
- [Job offer vs startup vs master's](examples/job-offer-reasoning.md) -
  subjective path: auto-invocation from plain conversation, row-wise, no web,
  a near-tie verdict.

## Design notes

Product vision and scope live in [PRODUCT.md](PRODUCT.md). The short version:
one-shot propose plus a single confirm (fewer round-trips, one point of
control), anchored rubrics (reproducible scores, not "5 = good"), Claude
proposes and you edit (speed with control), configurable scoring fanout (the
speed/quality/cost lever).

Deferred, deliberately: sensitivity and tie analysis, per-cell evidence
notes, CSV/HTML/Sheets export.

## Contributing and license

PRs welcome - see [CONTRIBUTING.md](CONTRIBUTING.md) for the dev loop and the
design invariants. [MIT](LICENSE).
