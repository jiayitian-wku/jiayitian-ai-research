# Research Roadmap

This roadmap begins on September 7, 2026. Completion should be recorded only after the corresponding evidence, notes, code, or results exist in the repository.

## Research Operating Rhythm

- Record work in a daily log only on an active research day.
- Review progress, decisions, unresolved questions, and next priorities once per active research week.
- Follow the documented paper-reading and experiment-tracking workflows rather than recording unsupported summaries or untracked runs.
- Treat stages as evidence gates: revisit an earlier stage whenever later work exposes a missing prerequisite.

## Foundation Checkpoint

Before or during Stage 1, identify only the prerequisites needed for the current work: Python and PyTorch basics, Linux tooling, GPU/CUDA concepts, profiling, descriptive statistics, uncertainty, and introductory queueing concepts. Record genuine gaps in `AI_BACKLOG.md` instead of delaying all research until every prerequisite is mastered.

## Stage 1: Understand LLM Inference Fundamentals

**Objective:** Build a precise mental and technical model of modern LLM inference.

- [ ] Study prefill and autoregressive decoding.
- [ ] Understand attention, KV caching, memory use, and memory bandwidth.
- [ ] Study batching, continuous batching, and request lifecycle management.
- [ ] Define throughput, time to first token, time per output token, end-to-end latency, and tail latency.
- [ ] Record concepts and unresolved questions under `notes/concepts/`.

**Exit criterion:** A concise set of notes can explain the main inference stages, resource bottlenecks, and evaluation metrics without relying on unexplained terminology.

## Stage 2: Build a Literature Map

**Objective:** Identify the main research directions, representative papers, assumptions, and open gaps.

- [ ] Establish search terms and inclusion criteria.
- [ ] Collect work on output-length prediction, serving, batching, scheduling, and admission control.
- [ ] Follow `papers/README.md` and distinguish queued, skimmed, read, and verified papers.
- [ ] Summarize each reviewed paper in `papers/literature_matrix.md`.
- [ ] Group papers by problem definition and evaluation methodology.
- [ ] Identify claims that require reproduction or closer verification.

**Exit criterion:** The literature matrix covers the central approaches and clearly shows unresolved questions relevant to this project.

## Stage 3: Study LLM Serving and Scheduling Systems

**Objective:** Understand how real serving systems translate scheduling policies into GPU execution.

- [ ] Study representative open-source LLM serving systems.
- [ ] Trace request admission, queuing, batching, prefill, decoding, and completion.
- [ ] Compare scheduling policies and their implementation constraints.
- [ ] Examine KV-cache allocation, preemption, swapping, and memory pressure.
- [ ] Record architecture and scheduling notes under `notes/systems/` and `notes/scheduling/`.

**Exit criterion:** A documented system model identifies where a new prediction or scheduling component could be integrated and measured.

## Stage 4: Reproduce a Baseline

**Objective:** Establish a trustworthy, reproducible reference implementation and measurement pipeline.

- [ ] Select a baseline system and scheduling policy using explicit criteria.
- [ ] Pin software, model, dataset, workload, and hardware configurations.
- [ ] Implement repeatable setup and execution instructions under `baseline/`.
- [ ] Validate metrics and measurement boundaries.
- [ ] Reproduce reported or expected behavior without overstating agreement.

**Exit criterion:** Another researcher can run the baseline and obtain documented measurements within a stated tolerance or understand why reproduction differs.

## Stage 5: Design Experiments

**Objective:** Create experiments that isolate causal effects and answer specific research questions.

- [ ] Define hypotheses before running experiments.
- [ ] Assign each planned run an experiment ID and create its record before execution.
- [ ] Choose workloads, models, hardware, baselines, and metrics.
- [ ] Define ablations for prediction accuracy and scheduling behavior.
- [ ] Plan repeated trials, warm-up, random seeds, and uncertainty reporting.
- [ ] Specify failure cases, fairness measures, and tail-latency analysis.

**Exit criterion:** Each planned experiment has a hypothesis, controlled variables, metrics, execution procedure, and analysis plan.

## Stage 6: Develop and Evaluate Research Ideas

**Objective:** Develop original methods and evaluate whether they offer meaningful, reproducible improvements.

- [ ] Formulate candidate prediction and scheduling ideas.
- [ ] Implement the smallest testable version of each idea.
- [ ] Compare against strong baselines and oracle bounds where appropriate.
- [ ] Run ablations and sensitivity analyses.
- [ ] Analyze negative results and boundary conditions.
- [ ] Document limitations, threats to validity, and future work.

**Exit criterion:** The repository contains evidence sufficient to support or reject each evaluated claim, with code and configurations needed for reproduction.

## Publication and Reproducibility Checkpoint

This checkpoint is reached only after Stage 6 produces a defensible result; it does not assume that the result is positive or publication-ready.

- [ ] Define the paper's precise claim and scope from the available evidence.
- [ ] Prepare a reproducibility package covering code, configurations, environment, data provenance, and result provenance.
- [ ] Draft the problem statement, related work, method, evaluation, limitations, and threats to validity.
- [ ] Obtain advisor or peer feedback and revise unsupported claims.
- [ ] Select a suitable venue only after checking the contribution and evaluation against its current requirements.
- [ ] Preserve negative or inconclusive findings accurately.

**Exit criterion:** A reader can trace every central claim to reviewed literature, an identified experiment, a versioned configuration, and a documented result or analysis.
