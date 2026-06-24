# Synap Benchmark Results: Category Breakdown

Best Synap scores on LongMemEval and LoCoMo, broken down by category.
These are the category-level numbers behind the published headlines of
**LoCoMo 93.2%** and **LongMemEval 92.0%**.

## Methodology

- **Answer model:** `gpt-5-mini`
- **Judge:** binary CORRECT / WRONG (`gpt-5-mini`)
- **LoCoMo:** Cat 1-4 only; adversarial (Cat 5) excluded per industry
  convention (mem0, Zep, original LoCoMo paper). Full set, all 1,540 questions.
- **LongMemEval:** full 500-question set.

---

## LongMemEval: Overall 92.0%

Full benchmark: 500 questions across 6 categories.

| Category | Synap | Correct / n |
|---|---|---|
| single-session-user | **100.0%** | 70 / 70 |
| single-session-preference | **100.0%** | 30 / 30 |
| knowledge-update | **100.0%** | 78 / 78 |
| temporal-reasoning | **100.0%** | 133 / 133 |
| single-session-assistant | 87.5% | 49 / 56 |
| multi-session | 75.2% | 100 / 133 |
| **Overall** | **92.0%** | **460 / 500** |

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

- LongMemEval official distribution: multi-session 133, temporal-reasoning 133,
  knowledge-update 78, single-session-user 70, single-session-assistant 56,
  single-session-preference 30.
- LoCoMo Cat 1-4 official distribution: open-domain 841, multi-hop 321,
  single-hop 282, temporal 96 (adversarial 446 excluded).

Both produce the headline 93.2 / 92.0 at full benchmark scale.
