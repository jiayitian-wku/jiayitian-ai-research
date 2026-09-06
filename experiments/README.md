# Experiment Tracking Workflow

## Purpose

This file defines the minimum record required to connect a research question, experiment plan, execution, and result without implying that a planned experiment was completed.

## Experiment Identity and Status

- Use IDs in the form `EXP-YYYYMMDD-NNN`.
- Create `experiments/<experiment-id>.md` before the first run.
- Store small, reviewable summaries and plots under `results/<experiment-id>/`.
- Keep large raw outputs under `results/<experiment-id>/raw/`; that path is ignored by Git. Record their external location, checksum, and generation command in the experiment record.
- Use one status: `planned`, `running`, `completed`, `invalidated`, or `stopped`.
- `completed` means the planned runs and analysis are complete; it does not mean the hypothesis was supported.

## Experiment Index

Add a row when a real experiment record is created. Do not add placeholder experiments.

| ID | Research Question | Status | Plan / Record | Result Summary | Last Updated |
|---|---|---|---|---|---|

## Required Workflow

1. Link the experiment to a roadmap or backlog question.
2. State a falsifiable hypothesis before examining results.
3. Record controlled, independent, and dependent variables.
4. Pin model, workload, hardware, software, configuration, random seeds, warm-up, repetitions, and metric definitions.
5. Record exact execution commands or a stable script entry point.
6. Preserve failed and invalidated runs, including the reason they cannot support a claim.
7. Separate observations from interpretation and link every reported number to its source artifact.
8. Update the index and weekly review when the experiment changes status.

## Experiment Record Template

```markdown
# <Experiment ID>: <Short Name>

- Status: planned | running | completed | invalidated | stopped
- Research question / backlog ID:
- Created:
- Last updated:

## Hypothesis

## Motivation and Prior Evidence

## Variables and Controls

## Configuration

- Code revision:
- Model:
- Workload / dataset:
- Hardware:
- Software environment:
- Seeds, warm-up, and repetitions:
- Metrics and measurement boundaries:

## Procedure and Commands

## Expected Analysis

## Run Log and Deviations

## Results and Artifact Links

## Interpretation

## Limitations / Threats to Validity

## Decision and Next Action
```
