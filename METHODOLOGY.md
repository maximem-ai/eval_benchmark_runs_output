# Methodology

This document specifies exactly how the [headline results](RESULTS.md) were produced, and, just as importantly, documents the evaluation variables that make memory-benchmark numbers comparable or not. Memory benchmarks are unusually easy to misreport: the same system can swing 10 to 30 points depending on answer model, judge, ingestion granularity, and prompt. We state our choices so they can be checked.

---

## Benchmarks

### LongMemEval ([Wu et al., ICLR 2025](https://arxiv.org/abs/2410.10813))

500 human-curated questions over long, multi-session chat histories (~115K tokens in `LongMemEval_S`). It probes five core abilities plus abstention: information extraction, multi-session reasoning, temporal reasoning, knowledge updates, and preference recall. Primary metric: **LLM-as-a-judge accuracy**.

Category distribution (500 Q):

| Category | n |
|---|---:|
| multi-session | 133 |
| temporal-reasoning | 133 |
| knowledge-update | 78 |
| single-session-user | 70 |
| single-session-assistant | 56 |
| single-session-preference | 30 |

### LoCoMo ([Maharana et al., ACL 2024](https://arxiv.org/abs/2402.17753))

Very long-term conversations (avg. ~300 turns, ~9K tokens) with questions in five categories. Category 5 is **adversarial**: questions designed to be unanswerable. Following the convention established by mem0, Zep, and the original paper, we report **Cat 1-4** and exclude adversarial Cat 5 from the headline.

Cat 1-4 distribution:

| Category | n |
|---|---:|
| open-domain | 841 |
| multi-hop | 321 |
| single-hop | 282 |
| temporal | 96 |
| *(adversarial, excluded)* | *446* |

---

## Our configuration

| Variable | Value |
|---|---|
| LongMemEval answer-generation model | `gemini-3.8-flash` |
| LongMemEval judge model | `gpt-4o` |
| LongMemEval judge protocol | Binary yes / no against the gold answer, using LongMemEval's per-question-type judge prompts (`evaluate_qa.py`) |
| LongMemEval run date | October 2026 |
| LongMemEval scoring views | All 500 questions (478 correct); answerable questions, 30 abstention excluded (450 / 470) |
| LongMemEval retrieval metric | Recall@20: share of questions where `gpt-4o` judges at least one of the first 20 retrieved memories relevant to the question and gold answer. Abstention questions are excluded (470 questions). Retrieval run 2026-10-13, labels produced 2026-10-25 with the MemoryBench relevance prompt |
| LongMemEval answering prompt | [`results/longmemeval/LME_answering_prompt.md`](results/longmemeval/LME_answering_prompt.md), run with source-evidence chunks and memory metadata on, plus a `## Current date` section holding the question date |
| LongMemEval scope | Full 500-question set, all 6 categories |
| LoCoMo answer-generation model | `gpt-5-mini` |
| LoCoMo judge model | `gpt-5-mini` |
| LoCoMo judge protocol | Binary **CORRECT / WRONG** against the gold answer |
| LoCoMo scope | Cat 1-4; adversarial Cat 5 excluded |
| Datasets | `LongMemEval_S`, `locomo10` |
| Harness | [`maximem-ai/memory_and_context_eval_harness`](https://github.com/maximem-ai/memory_and_context_eval_harness) |

---

## Scope decisions

**Why the full LongMemEval set?** We report all 500 questions across all six categories, including the hardest (`multi-session`, `temporal-reasoning`), rather than a curated slice.

**Why exclude LoCoMo adversarial (Cat 5)?** Cat 5 questions are intentionally unanswerable, so they measure *abstention / refusal* rather than memory retrieval. Including or excluding them shifts the overall score by 10%+, and is the most common source of incomparable LoCoMo numbers. We exclude it to match mem0, Zep, and the original LoCoMo paper. Mem0 has [publicly noted](#the-vendor-landscape) cases of competitors including Cat 5 in self-reported figures, which inflates the headline.
---

## Why vendor scores differ

The single most important thing to understand about agent-memory leaderboards is that **the configuration moves the score as much as the system does.** Supermemory's own results move from 81.6% to 85.2% on LongMemEval *just by switching the answer model*. Synap's 95.6% uses `gemini-3.8-flash` as the answer model and `gpt-4o` as the judge. The judge matches the one Supermemory and Mastra report, but the answer model differs from every competitor's, so the comparison is not like for like. Below is a side-by-side of the variables that matter, compiled from each vendor's published papers, blogs, and evaluation code.

| Variable | Mem0 | Zep | Letta (MemGPT) | Supermemory | Why it matters |
|---|---|---|---|---|---|
| **Answer LLM** | `gpt-4o-mini` | `gpt-4o-mini` / `gpt-4o` | `gpt-4o-mini` | `gpt-4o` / `gpt-5` / Gemini-3 Pro | **High.** Newer/larger models score materially higher on identical data. |
| **Judge LLM** | `gpt-4o-mini` | `gpt-4o` (LongMemEval) / `gpt-4o-mini` (LoCoMo) | `gpt-4o-mini` | `gpt-4o` | **High.** Judges differ in false-positive/negative rates. |
| **Embedder** | `text-embedding-3-small` | BGE-m3 | file search | not disclosed | Medium. Drives retrieval quality. |
| **Reranker** | none / graph | BGE-m3 reranker | n/a | not disclosed | Medium. Second-stage precision. |
| **Ingestion** | message-by-message | full conversation via API | file-based | session-by-session | Medium. Changes how memories are structured and time-grounded. |
| **Adversarial (LoCoMo Cat 5)** | excluded | excluded | n/a | n/a | **High.** Inclusion shifts overall by 10%+. |
| **Runs / variance** | single | 10 runs, mean ± std | single | not reported | Medium. Single runs have real variance. |
| **Timestamp handling** | embedded in text | bitemporal (event + ingest time) | n/a | dual-layer (doc + event date) | **High** for temporal questions. |

Sources: Mem0 evaluation repo and paper; Zep [arXiv:2501.13956](https://arxiv.org/abs/2501.13956); Supermemory [research](https://supermemory.ai/research) / [memorybench](https://github.com/supermemoryai/memorybench); Letta leaderboard notes.

### The vendor landscape

- **Mem0** reports 94.4% on LongMemEval (top-200 retrieval, about 6,787 tokens per query), and says the score reflects its managed platform, which includes optimizations not in its open-source SDK. It does not state the answer or judge model. This is a vendor-reported score that we have not verified. Source: [docs.mem0.ai](https://docs.mem0.ai/core-concepts/memory-evaluation).
- **Zep vs Mem0** ran a public dispute over prompt and methodology: Mem0 alleged "prompt tampering" (a modified answer-gen prompt in Zep's rebuttal) and adversarial-category inclusion; Zep disputed Mem0's sequential-search implementation as inflating reported latency.
- **Letta (MemGPT)** could not be evaluated on LongMemEval by Zep because the framework did not support ingesting existing message histories; Letta has since launched its own leaderboard.

The takeaway: **cite the model, judge, scope, and source for every number**, which is what this repository does.

---

## Published competitor numbers (LongMemEval)

For reference, each vendor's best self-reported LongMemEval accuracy:

| System | Accuracy | Answer model | Judge | Source |
|---|:---:|---|---|---|
| **Synap** (all 500 questions) | **95.6%** | `gemini-3.8-flash` | `gpt-4o` | This repo |
| **Synap** (answerable questions) | 95.7% | `gemini-3.8-flash` | `gpt-4o` | This repo |
| Mastra | 94.87% | `gpt-5-mini` | `gpt-4o` | [mastra.ai/research/observational-memory](https://mastra.ai/research/observational-memory) |
| Hindsight (Vectorize) | 94.6% | Gemini 3 Pro Preview | Gemini (version not stated) | [agentmemorybenchmark.ai](https://agentmemorybenchmark.ai/dataset/longmemeval) (paper, Dec 2025: 91.4% with Gemini 3 Pro Preview) |
| Mem0 | 94.4% | not stated | not stated | [docs.mem0.ai](https://docs.mem0.ai/core-concepts/memory-evaluation) |
| EverMind | 94.0% | not stated | not stated | [evermind.ai](https://evermind.ai/) |
| Honcho | 90.4% | Claude Haiku 4.5 | not named | [honcho.dev/evals](https://honcho.dev/evals/) |
| Zep | 90.2% | `gpt-5.4` | `gpt-5.4` | [getzep.com/research](https://www.getzep.com/research/) |
| Supermemory | 85.2% | Gemini-3 Pro | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) (earlier accuracy figure) |
| Supermemory | 84.6% | `gpt-5` | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) (earlier accuracy figure) |
| Supermemory | 81.6% | `gpt-4o` | `gpt-4o` | [supermemory.ai/research](https://supermemory.ai/research) (earlier accuracy figure) |
| Zep (2025 paper) | 71.2% | `gpt-4o` | `gpt-4o` | [arXiv:2501.13956](https://arxiv.org/abs/2501.13956) |
| Letta (MemGPT) | no first-party score found | n/a | n/a | n/a |

All competitor figures are vendor-reported and were not verified by us. Supermemory's current research page reports 97% Recall@20 on LongMemEval-S (judge `gpt-4o`), a retrieval metric rather than answer accuracy, so it is not in the table. Figures were read from the vendor pages on 2026-10-24.

---

## Reproducing

1. Clone the harness: [`maximem-ai/memory_and_context_eval_harness`](https://github.com/maximem-ai/memory_and_context_eval_harness).
2. Download the datasets (`LongMemEval_S`, `locomo10`).
3. Ingest into Synap, then run the answer and judge passes: `gemini-3.8-flash` and `gpt-4o` for LongMemEval, `gpt-5-mini` for both on LoCoMo.
4. Score LongMemEval over all 500 questions; score LoCoMo over Cat 1-4.

The latest LongMemEval run is published in [`results/longmemeval/`](results/longmemeval/):

- `question_records_gemini-3.8-flash.json`: one record per question (500), with the question, gold answer, the memories Synap retrieved, the generated answer, and the judge verdict.
- `manifest_gemini-3.8-flash.json`: dataset variant, models, run date and prompt settings.
- `LME_answering_prompt.md`: the prompt the answer model received.

An earlier run with answer model `gpt-5.4` is in `question_records.json` and `manifest.json`.

A question counts as correct when its `judge.judge_raw` starts with "yes" (any case). That gives 478 / 500.
