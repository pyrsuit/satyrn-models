# Model benchmarking

Use the `satyrn-benchmark` package to benchmark a model with
[evalplus](https://github.com/evalplus/evalplus) (HumanEval+ and MBPP+) on a cloud GPU.

Given a Hugging Face repo of raw safetensors weights, the pipeline:

1. installs and starts an [Ollama](https://ollama.com) server,
2. downloads the checkpoint and converts it to GGUF with llama.cpp,
3. registers the GGUF file as an Ollama model,
4. runs each configured evalplus dataset against it,
5. writes logs, samples, scores and a summary under `results/evalplus/`.

## Run it

Install the package on the GPU machine and call the CLI:

```sh
pip install -e ./benchmark
satyrn-benchmark --model mellum2-12b-a2.5
```

Ollama and the llama.cpp toolchain are installed on the first run. For back-to-back runs on
the same machine, pass `--no-install-deps` to skip that step:

```sh
satyrn-benchmark --model gemma-4-26b-a4b-it --no-install-deps
```

## Change what gets benchmarked

The configuration lives in `benchmark/src/satyrn/benchmark/config.py`. `MODELS` holds the models you can pick:

| `--model` | Hugging Face ref |
| --- | --- |
| `mellum2-12b-a2.5` | `hf.co/JetBrains/Mellum2-12B-A2.5B-Instruct` |
| `qwen3.6-27b` | `hf.co/Qwen/Qwen3.6-27B` |
| `gemma-4-26b-a4b-it` | `hf.co/google/gemma-4-26B-A4B-it` |

## Output

Under `results_dir` (default `results/evalplus/`):

- `<dataset>/<model>_openai_temp_0.0.jsonl` — the generated samples
- `<dataset>/<model>_openai_temp_0.0_eval_results.json` — per-problem scores
- `logs/<model>_<dataset>.log` — the full evalplus output, including `pass@k`
- `<model>_summary.txt` — status, result paths and `pass@k` for every dataset
