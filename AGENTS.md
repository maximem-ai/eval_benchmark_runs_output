# AGENTS.md

## What this repo is
Published benchmark results for Maximem Synap on LongMemEval and LoCoMo, with methodology and citation data. It holds documents only, no code. The numbers are produced by the separate open eval harness.

## Build, test, lint
Nothing to build. Before proposing a change, check that every number in `README.md` matches `RESULTS.md`, and that all links resolve.

## Layout
- `README.md`: headline numbers, comparison, reproduce steps
- `RESULTS.md`: category breakdown and counts
- `METHODOLOGY.md`: models, scope decisions, vendor comparison
- `bench/README.md`: how to re-run with the harness
- `CITATION.cff`: citation metadata
- `.github/`: banners, chart, templates

## Rules for changes
- Never change a score without a re-run from the harness and an updated `RESULTS.md`.
- Every claim about another vendor needs a source link.
- Do not add "#1" or "best" claims that this repo cannot back with a run.
- PRs must fill the AI-use section of the PR template.
- Write plain text: no em-dashes.
