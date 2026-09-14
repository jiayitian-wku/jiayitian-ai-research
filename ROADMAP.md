# Research Roadmap

This roadmap began on September 7, 2026. A task is marked complete only when its supporting notes, code, configuration, data, or results exist in the repository or are linked from it.

## Evidence Rules and Research Rhythm

- Record work in a daily log only on an active research day.
- Review progress, decisions, unresolved questions, and priorities once per active research week.
- Follow the documented paper-reading and experiment-tracking workflows rather than recording unsupported summaries or untracked runs.
- Treat stages as evidence gates and revisit an earlier stage when later work exposes a missing prerequisite.
- Preserve source facts, personal interpretations, research hypotheses, and experiment results as distinct categories.

## Overall Research Path

```text
Real serving systems
    → representative papers
    → prediction–scheduler interface
    → baselines and experiment design
    → implementation and verification
```

The project will not extend the toy scheduler blindly or present an unverified intuition as a research conclusion.

## Status Summary

### Completed

- Initialized the original research repository structure and workflow documents.
- Created the roadmap, backlog, daily-log template, literature matrix, paper workflow, experiment workflow, and weekly-review workflow.
- Defined the current focus as output-length prediction for LLM inference scheduling.
- Recorded the central research question and predictor-to-scheduler information flow.
- Registered the initial vLLM, SGLang, S3, and TIE source entry points.
- Created the first evidence-bounded daily log on September 14, 2026.

### In Progress

- Consolidating first-pass LLM serving notes into a version-specific system model.
- Completing repository and research-background organization.

### Planned

- Complete the S3 and TIE literature review.
- Identify a concrete serving-system integration point.
- Select and reproduce baselines.
- Design, run, and analyze versioned experiments.
- Develop and evaluate original research ideas only after the evidence gates are met.

## Foundation Checkpoint

Identify only the prerequisites needed for current work: Python and PyTorch basics, Linux tooling, GPU/CUDA concepts, profiling, descriptive statistics, uncertainty, and introductory queueing concepts. Record genuine gaps in `AI_BACKLOG.md` instead of delaying all research until every prerequisite is mastered.

## Stage 0: Repository and Research Background

**Status: In Progress**

- [x] Initialize the research repository structure.
- [x] Establish research-integrity, Git, paper-reading, experiment, daily-log, and weekly-review rules.
- [x] Record the current research direction and central questions.
- [x] Register the four initial source entry points.
- [x] Create `daily_logs/2026-09-14.md` and `docs/research_log.md`.
- [ ] Add traceable documentation or surviving artifacts for the historical toy-scheduler work.

**Exit criterion:** The repository structure, research scope, source entry points, and surviving prior work are documented without overstating progress.

## Stage 1: Understand LLM Inference and Serving Fundamentals

**Status: In Progress — Current Focus**

### Completed artifacts

- [x] Create first-pass, source-linked notes on Prefill and Decode.
- [x] Create first-pass, source-linked notes on KV Cache and PagedAttention.
- [x] Create first-pass, source-linked notes on fixed and continuous batching.
- [x] Create first-pass, source-linked introductions to vLLM and SGLang.

### Remaining work

- [ ] Verify the notes against pinned vLLM and SGLang versions and relevant source code.
- [ ] Define throughput, TTFT, TPOT/ITL, end-to-end latency, tail latency, GPU utilization, memory pressure, and fairness with explicit measurement boundaries.
- [ ] Trace request admission, queuing, batch formation, Prefill, Decode, completion, KV allocation, and preemption.
- [ ] Consolidate durable concepts and unresolved questions under `notes/concepts/`, `notes/systems/`, and `notes/scheduling/`.

**Exit criterion:** A concise, source-linked system model explains the main inference stages, resource bottlenecks, metrics, and candidate scheduling intervention points without unexplained terminology.

## Stage 2: Build the Literature Map

**Status: Planned — Formal Review Not Yet Started**

- [ ] Establish search terms and inclusion criteria.
- [ ] Read and document S3.
- [ ] Read and document TIE.
- [ ] Compare their research questions, prediction signals, scheduling decisions, uncertainty handling, and evaluation methods.
- [ ] Collect related work on output-length prediction, serving, batching, scheduling, and admission control.
- [ ] Follow `papers/README.md` and distinguish queued, skimmed, read, and verified papers.
- [ ] Update `papers/literature_matrix.md` and `docs/03_literature_map/` only after checking primary sources.
- [ ] Identify claims that require reproduction or closer verification.

**Exit criterion:** The literature matrix covers the central approaches and exposes the open questions relevant to this project.

## Stage 3: Define the Prediction–Scheduler Interface

**Status: Planned**

- [ ] Decide whether the predictor provides a point estimate, relative ranking, interval, probability distribution, or another uncertainty representation.
- [ ] Identify whether the prediction affects queue priority, admission, batch composition, KV Cache safety margins, routing, or preemption.
- [ ] Determine whether the implementation belongs inside a serving scheduler or in an external router.
- [ ] Separate prediction metrics, scheduling metrics, and end-to-end system metrics.
- [ ] Document implementation constraints in the selected vLLM or SGLang version.

**Exit criterion:** A documented interface states what information is available, when it is produced, which decision consumes it, and how its cost and errors will be measured.

## Stage 4: Reproduce Baselines

**Status: Planned**

- [ ] Select a baseline system and scheduling policies using explicit, literature-backed criteria.
- [ ] Pin software, model, dataset, workload, hardware, and random-seed configurations.
- [ ] Implement repeatable setup and execution instructions under `baseline/`.
- [ ] Include suitable FCFS, oracle-SJF, and predicted-length comparisons where justified.
- [ ] Validate metrics and measurement boundaries.
- [ ] Reproduce reported or expected behavior without overstating agreement.

**Exit criterion:** Another researcher can run the baseline and obtain documented measurements within a stated tolerance, or understand why reproduction differs.

## Stage 5: Design and Run Experiments

**Status: Planned**

- [ ] Define hypotheses and success criteria before running experiments.
- [ ] Assign each run an experiment ID and create its record before execution.
- [ ] Choose workloads, models, hardware, baselines, metrics, and prediction-error conditions.
- [ ] Evaluate numerical prediction error, ranking quality, uncertainty or calibration, and scheduling outcomes separately.
- [ ] Plan repeated trials, warm-up, random seeds, ablations, and uncertainty reporting.
- [ ] Specify failure cases, fairness measures, and tail-latency analysis.
- [ ] Preserve raw data before cleaning, aggregation, visualization, or interpretation.

**Exit criterion:** Every experiment has a hypothesis, controlled variables, configuration, execution procedure, raw-data location, metrics, and analysis plan.

## Stage 6: Develop and Evaluate Research Ideas

**Status: Planned**

- [ ] Formulate candidate prediction and scheduling ideas.
- [ ] Implement the smallest testable version of each idea without overwriting earlier versions.
- [ ] Compare against strong baselines and oracle bounds where appropriate.
- [ ] Run ablations and sensitivity analyses.
- [ ] Analyze negative, inconclusive, and boundary-case results.
- [ ] Document limitations, threats to validity, and future work.

**Exit criterion:** The repository contains enough evidence to support or reject each evaluated claim, with code and configurations needed for reproduction.

## Historical Toy Scheduler Boundary

The earlier FCFS/SJF toy-scheduling work is retained as learning material. It did not model KV Cache, continuous batching, Prefill/Decode interaction, GPU memory, preemption, or real parallel execution. Until its surviving code, configuration, and outputs are documented, it is not a formal baseline or experiment result.

## Publication and Reproducibility Checkpoint

This checkpoint is reached only after Stage 6 produces a defensible result; it does not assume a positive or publication-ready outcome.

- [ ] Define the paper's precise claim and scope from available evidence.
- [ ] Prepare a reproducibility package covering code, configurations, environment, data provenance, and result provenance.
- [ ] Draft the problem statement, related work, method, evaluation, limitations, and threats to validity.
- [ ] Obtain advisor or peer feedback and revise unsupported claims.
- [ ] Select a suitable venue only after checking the contribution and evaluation against its current requirements.
- [ ] Preserve negative or inconclusive findings accurately.

**Exit criterion:** A reader can trace every central claim to reviewed literature, an identified experiment, a versioned configuration, and a documented result or analysis.
