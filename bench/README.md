# Re-run the benchmarks

Every number in the root [README](../README.md) comes from the open
[eval harness](https://github.com/maximem-ai/memory_and_context_eval_harness).
There is no one-line bench command yet. You run the harness dashboard and start
the run from there.

## Settings used for the published results

- LongMemEval: `LongMemEval_S`, all 500 questions. Answer model `gpt-5.4`, judge `gpt-4o` (binary yes / no using LongMemEval's per-question-type judge prompts)
- LoCoMo: `locomo10`, Cat 1-4 only (1,540 questions), adversarial Cat 5 excluded. Answer model and judge both `gpt-5-mini`
- One run per benchmark
- Datasets: `LongMemEval_S`, `locomo10`
- LongMemEval answering prompt: [`results/longmemeval/LME_answering_prompt.md`](../results/longmemeval/LME_answering_prompt.md). The published run, prompts and per-question records are in [`results/longmemeval/`](../results/longmemeval/)

## Steps

```bash
git clone https://github.com/maximem-ai/memory_and_context_eval_harness.git
cd memory_and_context_eval_harness
python -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
cp .env.example .env     # set OPENAI_API_KEY and your Synap API key
python scripts/download_datasets.py --variant s
python -m runner.server  # http://localhost:8766
```

Select the Synap provider, use a Synap orchestrator config with the models listed above for each benchmark, and
run each benchmark. Expect scores within normal run-to-run variance, not an
exact match. See [METHODOLOGY.md](../METHODOLOGY.md) for what moves scores.

## Competitors

We did not re-run other vendors. Their numbers in the README are their own
published results, with the source linked. Their harness configs are in the
harness repo under `configs/` (`mem0.yaml`, `zep.yaml`, `supermemory.yaml`) if
you want to run them under the same settings.
