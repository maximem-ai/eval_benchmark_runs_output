<div align="center">

# Synap: Agent Memory Benchmark Results

*Your AI agents forget. Synap makes them remember.*

**Reproducible benchmark results for [Maximem Synap](https://www.maximem.ai/synap), the memory layer for production AI agents.**

[![LoCoMo](https://img.shields.io/badge/LoCoMo-93.2%25-2ea44f?style=for-the-badge)](RESULTS.md#locomo-overall-932-cat-1-4-adversarial-excluded)
[![LongMemEval](https://img.shields.io/badge/LongMemEval-92.0%25-2ea44f?style=for-the-badge)](RESULTS.md#longmemeval-overall-920)
[![Recall latency](https://img.shields.io/badge/P50%20recall-~15ms-1f6feb?style=for-the-badge)](https://www.maximem.ai/synap)
[![Models](https://img.shields.io/badge/models-gpt--5--mini-1f6feb?style=for-the-badge)](METHODOLOGY.md)
[![License](https://img.shields.io/badge/license-CC%20BY%204.0-lightgrey?style=for-the-badge)](LICENSE)

</div>

---

## TL;DR

| Benchmark | Synap | Scope |
|---|:---:|---|
| **LoCoMo** | **93.2%** | Cat 1-4; adversarial (Cat 5) excluded, per [industry convention](METHODOLOGY.md#scope-decisions) |
| **LongMemEval** | **92.0%** | Full 500-question set |

Both the answer model and the LLM-as-judge are `gpt-5-mini`. Full category-level breakdown lives in **[RESULTS.md](RESULTS.md)**; full methodology, models, and the variables that move scores across vendors are in **[METHODOLOGY.md](METHODOLOGY.md)**.

---

## How Synap compares

Memory benchmark scores are **highly sensitive to answer model, judge, ingestion granularity, and prompt**, so a fair comparison fixes those variables and cites every number. The table below lists each system's **own published LongMemEval accuracy** alongside the answer model it used (the single largest score driver).

| System | LongMemEval | Answer model | Source |
|---|:---:|---|---|
| **Synap** | **92.0%** | `gpt-5-mini` | This repo |
| Supermemory | 85.2% | Gemini-3 Pro | [supermemory.ai/research](https://supermemory.ai/research) |
| Supermemory | 84.6% | `gpt-5` | [supermemory.ai/research](https://supermemory.ai/research) |
| Zep | 71.2% | `gpt-4o` | [arXiv:2501.13956](https://arxiv.org/abs/2501.13956) |
| Mem0 | *not published* | n/a | n/a |
| Letta (MemGPT) | *not published* | n/a | n/a |

> **The lead comes from the memory layer, not a bigger model.** Synap reaches **92.0%** using `gpt-5-mini`, ahead of every published competitor result, including those generated with larger answer models (Supermemory's 85.2% on Gemini-3 Pro and 84.6% on `gpt-5`). Mem0 and Letta have not published LongMemEval results; both Zep and Supermemory have [publicly called on Mem0](METHODOLOGY.md#the-vendor-landscape) to evaluate on it.

```mermaid
xychart-beta
    title "LongMemEval: Published Accuracy (vendor self-reported)"
    x-axis ["Synap (gpt-5-mini)", "Supermemory (Gemini-3)", "Supermemory (gpt-5)", "Zep (gpt-4o)"]
    y-axis "Accuracy (%)" 0 --> 100
    bar [92.0, 85.2, 84.6, 71.2]
```

---

## Results by category

### LongMemEval: 92.0% (460 / 500)

| Category | Synap | Correct / n |
|---|:---:|:---:|
| single-session-user | **100.0%** | 70 / 70 |
| single-session-preference | **100.0%** | 30 / 30 |
| knowledge-update | **100.0%** | 78 / 78 |
| temporal-reasoning | **100.0%** | 133 / 133 |
| single-session-assistant | 87.5% | 49 / 56 |
| multi-session | 75.2% | 100 / 133 |
| **Overall** | **92.0%** | **460 / 500** |

### LoCoMo: 93.2% (Cat 1-4, adversarial excluded)

| Category | Synap |
|---|:---:|
| Multi-hop | **97.3%** |
| Open-domain | **93.4%** |
| Temporal | 90.8% |
| Single-hop | 88.8% |
| **Overall** | **93.2%** |

See **[RESULTS.md](RESULTS.md)** for the official-distribution counts behind each cell.

---

## About Synap

Your AI agents forget everything between conversations. Synap fixes that with persistent memory and context that works **across sessions, channels, and months, not just the last twenty turns**, and it plugs into every popular agent framework.

> Memory is not a storage problem alone. It is an active context-management problem. A vector database is not memory: retrieval finds text that *looks* similar, while memory maintains what is **true, current, and yours**.

Synap is an agentic context-management system built around three functions:

- **Capture what matters.** Structured extraction of facts, preferences, episodes, and temporal events, with entity resolution that maps "John", "John Smith", and "my manager" to one canonical record.
- **Compact without signal loss.** Adaptive compaction keeps context lean as conversations grow, so cost does not balloon and quality does not rot.
- **Recall when it is actually needed.** Context is pre-fetched before your agent asks, returning in **~15ms at P50**, fast enough to keep a voice agent conversational on a 300ms budget.

It ships with **native multi-agent support** (not retrofitted) and **Intelligent & Automated Context Scoping (IACS)**, which isolates user-level context while preserving shared organizational knowledge.

**Native framework integrations:** LangChain, LangGraph, LlamaIndex, CrewAI, Haystack, Google ADK, AutoGen, OpenAI Agents, Semantic Kernel, Pydantic AI, and more.

**Learn more:** [Website](https://www.maximem.ai/synap) · [Docs](https://www.maximem.ai/docs) · [Dashboard](https://synap.maximem.ai) · [SDK](https://github.com/maximem-ai/maximem_synap_sdk) · [How it works](https://www.maximem.ai/blog/how-maximem-synap-works)

```bash
pip install maximem-synap          # Python
npm install @maximem/synap-js-sdk  # JavaScript / TypeScript
```

---

## Methodology in brief

- **Answer model:** `gpt-5-mini`. **Judge:** binary CORRECT / WRONG, `gpt-5-mini`.
- **LongMemEval:** full 500-question set across 6 categories ([Wu et al., ICLR 2025](https://arxiv.org/abs/2410.10813)).
- **LoCoMo:** Cat 1-4 only; adversarial (Cat 5) excluded, matching the convention used by mem0, Zep, and the original LoCoMo paper ([Maharana et al., ACL 2024](https://arxiv.org/abs/2402.17753)).

Full details (datasets, scope decisions, and a side-by-side of *why vendor scores differ*: eval-LLM, judge, embedder, reranker, ingestion granularity, prompt, adversarial handling, run variance) are in **[METHODOLOGY.md](METHODOLOGY.md)**.

---

## Reproducibility

These results are produced with Maximem's open eval tooling against the **official public dataset distributions**, with no custom subsets or relabeling:

- **Harness:** [`maximem-ai/memory_and_context_eval_harness`](https://github.com/maximem-ai/memory_and_context_eval_harness)
- **Datasets:** LongMemEval (`LongMemEval_S`, 500 Q) and LoCoMo (`locomo10`), official releases.
- **Answer model:** `gpt-5-mini`. **Judge model:** `gpt-5-mini` (binary CORRECT / WRONG).
- **Scope:** LongMemEval full set; LoCoMo Cat 1-4 (adversarial Cat 5 excluded).

Per-run artifacts (answers, retrieved context, judge verdicts) are maintained by the Maximem evaluation team and available on request.

---

## What's in this repo

| File | Contents |
|---|---|
| [`README.md`](README.md) | This overview: headlines, comparison, category tables, about Synap. |
| [`RESULTS.md`](RESULTS.md) | Category-level breakdown with official-distribution counts. |
| [`METHODOLOGY.md`](METHODOLOGY.md) | Models, scope decisions, and the cross-vendor config comparison. |
| [`CITATION.cff`](CITATION.cff) | Machine-readable citation metadata. |

---

## Citing these results

If you reference these numbers, please cite the repository (see [`CITATION.cff`](CITATION.cff)) and the underlying benchmarks:

```bibtex
@inproceedings{wu2025longmemeval,
  title     = {LongMemEval: Benchmarking Chat Assistants on Long-Term Interactive Memory},
  author    = {Wu, Di and others},
  booktitle = {International Conference on Learning Representations (ICLR)},
  year      = {2025},
  eprint    = {2410.10813},
  archivePrefix = {arXiv}
}

@inproceedings{maharana2024locomo,
  title     = {Evaluating Very Long-Term Conversational Memory of LLM Agents},
  author    = {Maharana, Adyasha and others},
  booktitle = {Annual Meeting of the Association for Computational Linguistics (ACL)},
  year      = {2024},
  eprint    = {2402.17753},
  archivePrefix = {arXiv}
}
```

---

<div align="center">
<sub>Maintained by <a href="https://github.com/maximem-ai">Maximem</a>. Synap is the memory layer for production AI agents. <a href="https://github.com/maximem-ai/maximem_synap_sdk">Get the SDK</a>.</sub>
</div>
