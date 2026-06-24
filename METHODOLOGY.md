# Methodology

This document specifies exactly how the [headline results](RESULTS.md) were produced, and — just as importantly — documents the evaluation variables that make memory-benchmark numbers comparable or not. Memory benchmarks are unusually easy to misreport: the same system can swing 10–30 points depending on answer model, judge, ingestion granularity, and prompt. We state our choices so they can be checked.

---

## Benchmarks

### LongMemEval ([Wu et al., ICLR 2025](https://arxiv.org/abs/2410.10813))

500 human-curated questions over long, multi-session chat histories (~115K tokens in `LongMemEval_S`). It probes five core abilities plus abstention: information extraction, multi-session reasoning, temporal reasoning, knowledge updates, and preference recall. Primary metric: **LLM-as-a-judge accuracy**.

Official category distribution (500 Q):

| Category | n |
|---|---:|
| multi-session | 133 |
| temporal-reasoning | 133 |
| knowledge-update | 78 |
| single-session-user | 70 |
| single-session-assistant | 56 |
| single-session-preference | 30 |

### LoCoMo ([Maharana et al., ACL 2024](https://arxiv.org/abs/2402.17753))

Very long-term conversations (avg. ~300 turns, ~9K tokens) with questions in five categories. Category 5 is **adversarial** — questions designed to be unanswerable. Following the convention established by mem0, Zep, and the original paper, we report **Cat 1–4** and exclude adversarial Cat 5 from the headline.

Official Cat 1–4 distribution:

| Category | n |
|---|---:|
| open-domain | 841 |
| multi-hop | 321 |
| single-hop | 282 |
| temporal | 96 |
| *(adversarial — excluded)* | *446* |

---

## Our configuration

| Variable | Value |
|---|---|
| Answer-generation model | `gpt-5` |
| Judge model | `gpt-5-mini` |
| Judge protocol | Binary **CORRECT / WRONG** against the gold answer |
| LongMemEval scope | Full 500-question set, all 6 categories |
| LoCoMo scope | Cat 1–4; adversarial Cat 5 excluded |
| Datasets | Official public distributions (`LongMemEval_S`, `locomo10`) — no custom subsets, no relabeling |
| Harness | [`maximem-ai/memory_and_context_eval_harness`](https://github.com/maximem-ai/memory_and_context_eval_harness) |

---

## Scope decisions

**Why exclude LoCoMo adversarial (Cat 5)?** Cat 5 questions are intentionally unanswerable, so they measure *abstention/refusal* rather than memory retrieval. Including or excluding them shifts the overall score by 10%+ and is the most common source of incomparable LoCoMo numbers. We exclude it to match mem0, Zep, and the original LoCoMo paper. Mem0 has [publicly noted](#the-vendor-landscape) cases of competitors including Cat 5 in self-reported figures, which inflates the headline.

**Why the full LongMemEval set?** We report all 500 questions across all six categories — including the hardest (`multi-session`, `temporal-reasoning`) — rather than a curated slice, so the overall figure reflects the official distribution.

---

## Why vendor scores differ

The single most important thing to understand about agent-memory leaderboards is that **the configuration moves the score as much as the system does.** Supermemory's own results move from 81.6% → 85.2% on LongMemEval *just by switching the answer model*. Below is a side-by-side of the variables that matter, compiled from each vendor's published papers, blogs, and evaluation code.

| Variable | Mem0 | Zep | Letta (MemGPT) | Supermemory | Why it matters |
|---|---|---|---|---|---|
| **Answer LLM** | `gpt-4o-mini` | `gpt-4o-mini` / `gpt-4o` | `gpt-4o-mini` | `gpt-4o` / `gpt-5` / Gemini-3 Pro | **High** — newer/larger models score materially higher on identical data. |
| **Judge LLM** | `gpt-4o-mini` | `gpt-4o-mini` (LoCoMo) / `gpt-4o` (LongMemEval) | `gpt-4o-mini` | `gpt-4o` | **High** — judges differ in false-positive/negative rates. |
| **Embedder** | `text-embedding-3-small` | BGE-m3 | file search | not disclosed | Medium — drives retrieval quality. |
| **Reranker** | none / graph | BGE-m3 reranker | n/a | not disclosed | Medium — second-stage precision. |
| **Ingestion** | message-by-message | full conversation via API | file-based | session-by-session | Medium — changes how memories are structured and time-grounded. |
| **Adversarial (LoCoMo Cat 5)** | excluded | excluded | — | n/a | **High** — inclusion shifts overall by 10%+. |
| **Runs / variance** | single | 10 runs, mean ± std | single | not reported | Medium — single runs have real variance. |
| **Timestamp handling** | embedded in text | bitemporal (event + ingest time) | n/a | dual-layer (doc + event date) | **High** for temporal questions. |

Sources: Mem0 evaluation repo and paper; Zep [arXiv:2501.13956](https://arxiv.org/abs/2501.13956); Supermemory [research](https://supermemory.ai/research) / [memorybench](https://github.com/supermemoryai/memorybench); Letta leaderboard notes.

### The vendor landscape

- **Mem0** has not published LongMemEval results. Both Zep and Supermemory have publicly encouraged Mem0 to evaluate on it.
- **Zep vs Mem0** ran a public dispute over prompt and methodology: Mem0 alleged "prompt tampering" (a modified answer-gen prompt in Zep's rebuttal) and adversarial-category inclusion; Zep disputed Mem0's sequential-search implementation as inflating reported latency.
- **Letta (MemGPT)** could not be evaluated on LongMemEval by Zep because the framework did not support ingesting existing message histories; Letta has since launched its own leaderboard.

The takeaway: **cite the model, judge, scope, and source for every number** — which is what this repository does.

---

## Published competitor numbers (LongMemEval)

For reference, each vendor's best self-reported LongMemEval accuracy:

| System | Accuracy | Answer model | Judge | Source |
|---|:---:|---|---|---|
| **Synap** | **92.0%** | `gpt-5` | `gpt-5-mini` | This repo |
| Supermemory | 85.2% | Gemini-3 Pro | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) |
| Supermemory | 84.6% | `gpt-5` | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) |
| Supermemory | 81.6% | `gpt-4o` | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) |
| Zep | 71.2% | `gpt-4o` | `gpt-4o` | [arXiv:2501.13956](https://arxiv.org/abs/2501.13956) |
| Mem0 | not published | — | — | — |
| Letta (MemGPT) | not published | — | — | — |

---

## Reproducing

1. Clone the harness: [`maximem-ai/memory_and_context_eval_harness`](https://github.com/maximem-ai/memory_and_context_eval_harness).
2. Download the official datasets (`LongMemEval_S`, `locomo10`).
3. Ingest into Synap, then run the answer + judge pass with `gpt-5` / `gpt-5-mini`.
4. Score LongMemEval over all 500 questions; score LoCoMo over Cat 1–4.

Per-run artifacts (generated answers, retrieved context, per-question judge verdicts) are maintained by the Maximem evaluation team and available on request.
