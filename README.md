# LLM Inference and GPU Scheduling Research

## Research Topic

This repository documents a research project on large language model (LLM) inference, with a particular focus on:

- output-length prediction;
- GPU resource scheduling;
- serving throughput;
- end-to-end and per-request latency; and
- the trade-offs among efficiency, predictability, and fairness.

The research journey begins on September 7, 2026. Notes, experiments, and conclusions will be added only when the corresponding work has been completed.

## Current Research Stage

The project is at the initialization and planning stage. Stage 1—understanding LLM inference fundamentals—is the first active research stage. No baseline has been reproduced and no experimental results have been produced yet.

## Long-Term Goal

The long-term goal is to develop and rigorously evaluate practical ideas that use workload information, potentially including predicted output length, to improve GPU scheduling for LLM inference. A successful outcome should improve throughput and latency while accounting for prediction error, resource constraints, fairness, and reproducibility.

## Repository Structure

```text
.
├── README.md                    # Project overview and current status
├── ROADMAP.md                   # Research stages and milestones
├── AI_BACKLOG.md                # Open questions and tasks for AI-assisted work
├── daily_logs/                  # One research log per active research day
├── weekly_reviews/
│   └── README.md                # Weekly-review workflow and template
├── notes/
│   ├── concepts/                # Core inference and modeling concepts
│   ├── systems/                 # LLM serving-system notes
│   └── scheduling/              # Scheduling theory and policy notes
├── papers/
│   ├── README.md                # Paper-reading workflow and note template
│   └── literature_matrix.md     # Structured comparison of reviewed papers
├── baseline/                    # Baseline implementations and reproduction work
├── experiments/
│   └── README.md                # Experiment workflow, index, and record template
├── results/                     # Verified experimental outputs and analyses
├── src/                         # Reusable research code
└── .gitignore                   # Files intentionally excluded from version control
```

## How to Use This Repository

Use the repository as an evidence trail rather than a list of intended accomplishments:

1. Choose the next concrete task from `ROADMAP.md` or `AI_BACKLOG.md`.
2. On each active research day, create or update `daily_logs/YYYY-MM-DD.md` using the existing daily-log headings.
3. For a paper, follow `papers/README.md`; add claims to the literature matrix only after checking the paper itself.
4. Before running an experiment, assign an experiment ID and record its hypothesis, configuration, metrics, and analysis plan according to `experiments/README.md`.
5. At the end of an active research week, create `weekly_reviews/YYYY-Www.md` using the workflow in `weekly_reviews/README.md`.
6. Promote durable knowledge from daily logs into `notes/`, and link completed backlog items to the note, paper record, code, or experiment that resolves them.

Do not mark a roadmap or backlog item complete unless the supporting evidence exists in the repository or its external location is documented.

## Current Progress

- [x] Define the initial repository structure.
- [x] Create the roadmap, research backlog, daily-log template, and literature matrix.
- [x] Define paper-reading, experiment-tracking, and weekly-review workflows.
- [ ] Complete the first pass through LLM inference fundamentals.
- [ ] Build the initial literature map.
- [ ] Select and reproduce a baseline.
- [ ] Design and run experiments.
- [ ] Develop and evaluate original research ideas.

## Key Research Questions

1. Which signals available before or during decoding are most useful for predicting output length?
2. How accurate must an output-length predictor be before it improves a scheduling policy?
3. How should a scheduler react to uncertainty or systematic error in length predictions?
4. Which scheduling objectives best capture the trade-off among throughput, mean latency, tail latency, and fairness?
5. How do batching, prefill, decoding, KV-cache pressure, and memory bandwidth interact with scheduling decisions?
6. When do length-aware policies outperform FIFO, round-robin, shortest-job-first approximations, or existing serving policies?
7. How should experiments separate gains from scheduling decisions from gains caused by implementation details or hardware utilization?
8. What workload traces, models, hardware settings, and metrics are required for credible and reproducible evaluation?
