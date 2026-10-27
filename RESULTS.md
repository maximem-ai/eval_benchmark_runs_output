# Synap Benchmark Results: Category Breakdown

Synap scores on LongMemEval and LoCoMo, broken down by category.
These are the category-level numbers behind the published headlines of
**LongMemEval 96.1%** (answerable, excluding 3 dataset-defect questions), **LongMemEval 95.2%** (all 500 questions), **LongMemEval Recall@20 98.1%** and **LoCoMo 93.2%**.

## Methodology

- **LongMemEval:** answer model `gpt-5.4`, judge `gpt-4o` (binary yes / no).
- **LongMemEval:** full 500-question set.
- **LongMemEval retrieval (Recall@20):** `gpt-4o` relevance judge over the first 20 retrieved memories; abstention questions excluded (470 questions).
- **LoCoMo:** answer model `gpt-5-mini`, judge `gpt-5-mini` (binary CORRECT / WRONG).
- **LoCoMo:** Cat 1-4 only; adversarial (Cat 5) excluded per industry
  convention (mem0, Zep, original LoCoMo paper). Full set, all 1,540 questions.

---

## LongMemEval: Answerable, excluding 3 dataset-defect questions 96.1%

Three LongMemEval_S questions have gold answers the conversations do not support: `37f165cf`, `gpt4_731e37d7` and `7405e8b1` (the last is marked arguable). The run answered all three wrong.

| Set | Correct / total | Score |
|---|---|---|
| Answerable, excluding the 3 defect questions | 449 / 467 | **96.1%** |
| Answerable (30 abstention questions excluded) | 449 / 470 | 95.5% |
| All 500 questions | 476 / 500 | 95.2% |

**Answerable, excluding the 3 defect questions, by category**

| Category | Score | Correct / n |
|---|---|---|
| single-session-user | **100.0%** | 64 / 64 |
| knowledge-update | 98.6% | 71 / 72 |
| temporal-reasoning | 96.9% | 123 / 127 |
| single-session-preference | 96.7% | 29 / 30 |
| single-session-assistant | 96.4% | 54 / 56 |
| multi-session | 91.5% | 108 / 118 |
| **Overall** | **96.1%** | **449 / 467** |

**Answerable questions (30 abstention questions excluded), by category**

| Category | Score | Correct / n |
|---|---|---|
| single-session-user | **100.0%** | 64 / 64 |
| knowledge-update | 98.6% | 71 / 72 |
| temporal-reasoning | 96.9% | 123 / 127 |
| single-session-preference | 96.7% | 29 / 30 |
| single-session-assistant | 96.4% | 54 / 56 |
| multi-session | 89.3% | 108 / 121 |
| **Overall** | **95.5%** | **449 / 470** |

Each excluded question, with quotes: [`results/longmemeval/DATASET_DEFECTS.md`](results/longmemeval/DATASET_DEFECTS.md). The list as data: [`results/longmemeval/dataset_defects.json`](results/longmemeval/dataset_defects.json).

---

## LongMemEval: Overall 95.2%

Full benchmark: 500 questions across 6 categories.

| Category | Synap | Correct / n |
|---|---|---|
| single-session-user | **100.0%** | 70 / 70 |
| knowledge-update | 97.4% | 76 / 78 |
| temporal-reasoning | 97.0% | 129 / 133 |
| single-session-preference | 96.7% | 29 / 30 |
| single-session-assistant | 96.4% | 54 / 56 |
| multi-session | 88.7% | 118 / 133 |
| **Overall** | **95.2%** | **476 / 500** |

---

Per-question records for this run (question, gold answer, retrieved memories, generated
answer, judge verdict) are in [`results/longmemeval/question_records.json`](results/longmemeval/question_records.json).
Run details are in [`results/longmemeval/manifest.json`](results/longmemeval/manifest.json).

---

## LongMemEval retrieval: Recall@20 98.1%

A question is a hit when GPT-4o judges at least one of the first 20 retrieved memories relevant to the question and its gold answer. Abstention questions are excluded (30 of 500), which leaves 470.

| Category | Recall@20 | Hits / questions |
|---|---|---|
| single-session-user | **100.0%** | 64 / 64 |
| knowledge-update | 98.6% | 71 / 72 |
| multi-session | 98.3% | 119 / 121 |
| single-session-assistant | 98.2% | 55 / 56 |
| temporal-reasoning | 96.9% | 123 / 127 |
| single-session-preference | 96.7% | 29 / 30 |
| **Overall** | **98.1%** | **461 / 470** |

Per-question labels: [`results/longmemeval/retrieval_relevance_gpt4o_top20.jsonl`](results/longmemeval/retrieval_relevance_gpt4o_top20.jsonl). Totals: [`results/longmemeval/retrieval_relevance_gpt4o_top20.summary.json`](results/longmemeval/retrieval_relevance_gpt4o_top20.summary.json).

---

## LoCoMo: Overall 93.2% (Cat 1-4, adversarial excluded)

Full benchmark: 1,540 Cat 1-4 questions across 4 categories.

| Category | Synap |
|---|---|
| Multi-hop | **97.3%** |
| Open-domain | **93.4%** |
| Temporal | 90.8% |
| Single-hop | 88.8% |
| **Overall** | **93.2%** |

---

### Scope

- LongMemEval question counts: multi-session 133, temporal-reasoning 133,
  knowledge-update 78, single-session-user 70, single-session-assistant 56,
  single-session-preference 30.
- LoCoMo Cat 1-4 question counts: open-domain 841, multi-hop 321,
  single-hop 282, temporal 96 (adversarial 446 excluded).

Both produce the headline 95.2 / 93.2 at full benchmark scale. The retrieval headline of 98.1% is computed over the 470 non-abstention LongMemEval questions.
