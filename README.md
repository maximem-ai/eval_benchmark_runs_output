# Synap — Eval Benchmark Runs

Published benchmark results for [Maximem Synap](https://github.com/maximem-ai/maximem_synap_sdk),
the memory layer that makes AI agents remember.

## Headlines

| Benchmark | Synap | Scope |
|---|---|---|
| **LoCoMo** | **93.2%** | Cat 1–4, adversarial (Cat 5) excluded |
| **LongMemEval** | **92.0%** | Full 500-question set |

See **[RESULTS.md](RESULTS.md)** for the full category-level breakdown behind these headlines.

## Methodology

- **Answer model:** `gpt-5`
- **Judge:** binary CORRECT / WRONG (`gpt-5-mini`)
- **LoCoMo:** Cat 1–4 only — adversarial (Cat 5) excluded per industry convention
  (mem0, Zep, original LoCoMo paper). Full set — all 1,540 questions.
- **LongMemEval:** full 500-question set.

## Contents

- [`RESULTS.md`](RESULTS.md) — category breakdown for LongMemEval and LoCoMo.
