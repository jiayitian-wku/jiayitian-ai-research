# Research Log

This log records only work that is supported by files in the repository. Planned work is labeled explicitly and is not counted as completed research progress.

## 2026-09-14 — Research Direction and Repository Audit

### Completed

- Defined the current research direction as output-length prediction for LLM inference scheduling.
- Recorded the intended information flow: user request → length predictor → predicted output length → scheduler → GPU resource allocation.
- Created a research map that separates source-backed facts, current interpretation, unverified hypotheses, and future evaluation questions.
- Created first-pass, source-linked notes covering:
  - Prefill and Decode;
  - KV Cache growth and PagedAttention;
  - fixed request batches and continuous batching;
  - the high-level vLLM serving and scheduling path; and
  - the high-level SGLang runtime, RadixAttention, and overlap scheduler.
- Registered initial source links for vLLM, SGLang, S3, and TIE.
- Audited the repository structure, Git status, file types, recent modification times, ignored-file rules, and potential sensitive information.
- Updated the project README to describe the repository's actual documentation-only state and to avoid implying that a software or experiment pipeline already exists.
- Connected the local repository to the existing GitHub history at commit `9b6b783`, restored all remote-only workflow files, and merged the local documentation without committing or pushing.

### In Progress

- Connecting the serving concepts into a consistent model of how unknown output length may affect scheduling.
- Checking which decisions in a fixed vLLM or SGLang version could consume output-length information.

### Evidence Boundary

The repository did **not** contain the following items at the time of this audit:

- Python source files or notebooks;
- OpenAI API calls or API configuration;
- output-collection scripts;
- CSV, JSON, JSONL, or Parquet datasets;
- baseline predictor implementations;
- experiment scripts, logs, figures, or result files;
- Python dependency declarations; or
- saved evidence of throughput, latency, GPU-utilization, or prediction-accuracy improvements.

No model training, scheduling optimization, performance improvement, or final experiment conclusion is recorded as completed.

### Sensitive-Information Check

- No `.env`, credential, token, password, private-key, or API-key file was found in the current project tree.
- No hard-coded value matching the scanned API-key or access-token patterns was found in project files.
- The retained Git history tracks the 19 files from commit `9b6b783`; a filename-only secret-pattern scan found no hard-coded key or token in that remote baseline.
- `.env`, credential files, private keys, model weights, raw temporary results, and common caches are covered by `.gitignore` rules.

### Artifacts

- [`00_research_direction/research_map.md`](00_research_direction/research_map.md)
- [`01_serving_basics/prefill_decode.md`](01_serving_basics/prefill_decode.md)
- [`01_serving_basics/kv_cache.md`](01_serving_basics/kv_cache.md)
- [`01_serving_basics/batching.md`](01_serving_basics/batching.md)
- [`01_serving_basics/vllm.md`](01_serving_basics/vllm.md)
- [`01_serving_basics/sglang.md`](01_serving_basics/sglang.md)
- [`../RESEARCH_QUESTIONS.md`](../RESEARCH_QUESTIONS.md)
- [`../ROADMAP.md`](../ROADMAP.md)

### TODO / Next Steps

- Read and document S3 and TIE from primary sources.
- Fix the target serving framework version and inspect its scheduler interfaces.
- Define the predictor input/output contract and the output-collection schema.
- Implement and evaluate simple output-length prediction baselines.
- Compare FCFS, oracle-SJF, and predicted-length scheduling in a versioned simulation.
- Investigate how prediction accuracy and ranking quality affect throughput and latency.
- Preserve raw data before aggregation and generate visualizations only from traceable results.
