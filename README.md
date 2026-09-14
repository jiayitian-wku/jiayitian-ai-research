# LLM Inference and Output-Length Scheduling Research

An undergraduate research project investigating whether predicted output length can help an LLM inference scheduler make better resource-allocation decisions.

This repository is currently a **research knowledge base and planning workspace**. It does not yet contain a data-collection pipeline, a trained predictor, a scheduling implementation, or verified performance results.

The repository's documented research journey began on September 7, 2026. Notes, experiments, and conclusions are added only when the corresponding work has actually been completed.

## Research Topic

The project studies large language model inference with a particular focus on:

- output-length prediction;
- GPU resource scheduling;
- serving throughput;
- end-to-end and per-request latency; and
- trade-offs among efficiency, predictability, fairness, and reproducibility.

## Research Motivation

Autoregressive LLM serving processes a request through Prefill and iterative Decode steps. During Decode, a request continues to consume computation and KV Cache resources until it reaches a stopping condition. The true output length is normally unknown when the request arrives, so the scheduler does not directly know how long the request will remain active or when its resources will be released.

The working motivation is that an output-length estimate may provide useful information for request ordering, admission, batch composition, KV Cache planning, or preemption. Whether this information actually improves throughput or latency—and how prediction errors change the outcome—remains an open research question for this project, not an established result.

Background notes and their sources are recorded in [`docs/01_serving_basics/`](docs/01_serving_basics/) and [`references/sources.md`](references/sources.md).

## Research Question

> **Can output length prediction help improve LLM inference scheduling efficiency?**

The project will study this question through several related dimensions:

- how output-length prediction accuracy relates to scheduling decisions;
- whether relative request-length ordering matters independently of absolute prediction error;
- how prediction quality affects throughput, latency, GPU utilization, and fairness;
- which scheduler decision should consume the prediction; and
- how robust a length-aware policy is under prediction uncertainty.

The primary metrics, baselines, and success criteria have not yet been finalized.

## Current Pipeline

```text
User Request
    ↓
Length Predictor
    ↓
Predicted Output Length
    ↓
Scheduler
    ↓
GPU Resource Allocation
```

This diagram is the intended research pipeline. At present, it is a problem formulation rather than a completed software pipeline.

## Current Research Stage

The repository has completed its initial workflow setup and is now in the LLM-serving-foundations stage. Five first-pass notes exist, but their version-specific implementation details still need verification. No baseline has been reproduced and no experiment result has been produced.

## Long-Term Goal

The long-term goal is to develop and rigorously evaluate practical methods that use predicted output length or related uncertainty information to improve LLM inference scheduling. Any claimed improvement must be evaluated against documented baselines while accounting for prediction error, resource constraints, fairness, measurement boundaries, and reproducibility.

## Repository Structure

```text
.
├── README.md                      # Project overview and evidence-bounded status
├── ROADMAP.md                     # Research stages and milestones
├── AI_BACKLOG.md                  # Original backlog and open questions
├── RESEARCH_QUESTIONS.md          # Research questions and unverified hypotheses
├── PRO20X_QUEUE.md                # Placeholder for future task tracking
├── AGENTS.md                      # Research-integrity and repository rules
├── daily_logs/                    # Date-based records of work actually completed
├── weekly_reviews/                # Original weekly-review workflow
├── notes/                         # Original concept, system, and scheduling note structure
├── papers/                        # Original paper workflow and literature matrix
├── docs/
│   ├── 00_research_direction/     # Scope, pipeline, and research map
│   ├── 01_serving_basics/         # Prefill/Decode, KV Cache, batching, vLLM, SGLang
│   ├── 02_papers/                 # S3/TIE reading placeholders; not yet completed
│   ├── 03_literature_map/         # Literature-map placeholders
│   ├── 04_meeting/                # Meeting-briefing placeholder
│   └── research_log.md            # Chronological project progress log
├── references/                    # Links to papers and official sources
├── baseline/                      # Reserved for reproducible baseline work
├── experiments/README.md          # Original experiment-recording workflow
├── experiments/toy_scheduler/     # Reserved for traceable early toy-scheduler work
├── src/                           # Reserved for reusable implementation code
└── results/                       # Reserved for verified experiment outputs
```

The placeholder directories are intentionally retained. Their presence does not mean that implementations or results already exist.

## How to Use This Repository

Use the repository as an evidence trail rather than a list of intended accomplishments:

1. Select the next concrete task from `ROADMAP.md`, `AI_BACKLOG.md`, or the current research questions.
2. Record work in `daily_logs/YYYY-MM-DD.md` only when that work actually occurred.
3. Follow `papers/README.md` before adding a paper claim to a note or literature matrix.
4. Before an experiment, assign an experiment ID and record its hypothesis, configuration, metrics, and analysis plan using `experiments/README.md`.
5. Use `weekly_reviews/` to record completed work, unresolved questions, and next priorities for active research weeks.
6. Link completed roadmap or backlog items to the supporting note, code, configuration, raw data, or result.

## Current Progress

### Completed and present in the repository

- Created the original roadmap, backlog, daily-log template, literature matrix, paper workflow, experiment workflow, and weekly-review workflow.
- Defined the research scope around **output-length prediction for LLM inference scheduling**.
- Documented the predictor-to-scheduler research pipeline and the central research question.
- Created a first-pass set of source-linked notes on Prefill/Decode, KV Cache, fixed versus continuous batching, vLLM, and SGLang.
- Recorded the current research hypothesis that relative length ordering may matter in addition to absolute prediction error; it is explicitly marked as unverified.
- Registered initial official and paper sources for vLLM, SGLang, S3, and TIE.
- Established repository, research-integrity, and daily-recordkeeping rules.

### In progress

- Consolidating the first-pass LLM serving concepts into a consistent system model.
- Verifying current vLLM and SGLang scheduling and KV Cache behavior against fixed versions and source code.
- Preparing to read and compare S3 and TIE.

### Not yet implemented or verified

- OpenAI API integration or output-collection code;
- a collected dataset or dataset-generation pipeline;
- baseline or trained output-length predictors;
- predictor evaluation scripts or accuracy results;
- FCFS, SJF, or predicted-length scheduling implementations in this repository;
- reproducible serving experiments;
- throughput, latency, GPU-utilization, or fairness improvements; and
- final research conclusions.

## Detailed Research Questions

1. Which signals available before or during decoding are most useful for predicting output length?
2. How accurate must an output-length predictor be before it improves a scheduling policy?
3. How should a scheduler react to uncertainty or systematic error in length predictions?
4. Which scheduling objectives best capture trade-offs among throughput, mean latency, tail latency, and fairness?
5. How do batching, Prefill, Decode, KV Cache pressure, and memory bandwidth interact with scheduling decisions?
6. When do length-aware policies outperform FCFS, round-robin, oracle-SJF approximations, or existing serving policies?
7. How should experiments separate gains from scheduling decisions from gains caused by implementation details or hardware utilization?
8. Which workload traces, models, hardware settings, and metrics are required for credible and reproducible evaluation?

## How to Run

There is currently **no executable Python pipeline** in this repository. The project contains no Python scripts, no imported third-party packages, no `requirements.txt`, and no runtime environment that needs to be installed. Consequently, there is not yet a valid data-collection, training, prediction, scheduling, or evaluation command to run.

The current artifacts can be reviewed directly as Markdown, starting with:

1. [`docs/00_research_direction/research_map.md`](docs/00_research_direction/research_map.md)
2. [`docs/01_serving_basics/`](docs/01_serving_basics/)
3. [`RESEARCH_QUESTIONS.md`](RESEARCH_QUESTIONS.md)
4. [`ROADMAP.md`](ROADMAP.md)
5. [`docs/research_log.md`](docs/research_log.md)

No `.env` file or API key is required at the current stage. If API-based collection is implemented later, secrets must be loaded from a local `.env` or environment variables and must never be committed. `.env` files are already excluded by `.gitignore`.

When executable code is added, this section must be updated with the tested Python version, exact installation command, required environment variables, and verified entry-point commands.

## Next Steps

The following items are **planned work**, not completed results:

1. **TODO — Literature grounding:** read S3 and TIE and compare their prediction signals, scheduling decisions, uncertainty handling, and evaluation methods.
2. **TODO — System boundary:** select and pin a vLLM or SGLang version and identify a concrete scheduling intervention point.
3. **TODO — Data collection:** define a reproducible output-collection schema before collecting LLM outputs.
4. **TODO — Prediction baselines:** implement simple, evidence-backed output-length prediction baselines and compare prediction methods.
5. **TODO — Predictor evaluation:** evaluate numerical error, relative ordering quality, calibration or uncertainty, and workload-specific behavior.
6. **TODO — Scheduling simulation:** implement traceable FCFS, oracle-SJF, and predicted-length scheduling baselines without overwriting earlier versions.
7. **TODO — System evaluation:** measure throughput, TTFT, TPOT/ITL, end-to-end latency, GPU utilization, memory pressure, and fairness under documented workloads.
8. **TODO — Visualization:** generate figures only from preserved raw experiment data and versioned configurations.

## Evidence and Research Integrity

Repository statements distinguish source-backed facts, current interpretation, research hypotheses, and experiment results. No experiment result should be treated as verified unless its raw data, configuration, code version, and analysis are preserved and linked.
