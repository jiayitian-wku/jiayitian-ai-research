# AI Research Backlog

This file stores open research and engineering work. Items are questions or tasks, not established findings. AI may assist with an item, but sources, code, and conclusions must be independently checked.

## Backlog Rules

- IDs are stable: `LEARN` for knowledge gaps, `LIT` for literature searches, `CODE` for engineering work, `EXPQ` for experimental questions, and `SYN` for synthesis questions.
- Priority means `P0` (blocks the current stage), `P1` (important next work), or `P2` (later exploration).
- When an item becomes active, record its next action in the relevant daily or weekly log.
- Check an item only after linking it to a durable note, paper record, code artifact, experiment, or documented decision.

## Questions I Do Not Understand

- [ ] `LEARN-001` `[P0]` How do prefill and decoding differ in their compute, memory, and batching behavior?
- [ ] `LEARN-002` `[P0]` How does KV-cache growth constrain concurrency and scheduling choices?
- [ ] `LEARN-003` `[P0]` Which components dominate time to first token versus time per output token?
- [ ] `LEARN-004` `[P0]` How do continuous batching and iteration-level scheduling work in practice?
- [ ] `LEARN-005` `[P1]` Which fairness definitions are appropriate for heterogeneous LLM requests?
- [ ] `LEARN-006` `[P1]` How should uncertainty in predicted output length be represented and calibrated?

## Literature Search Topics

- [ ] `LIT-001` `[P0]` Foundational work on LLM inference and serving architectures.
- [ ] `LIT-002` `[P1]` Output-length and remaining-length prediction methods.
- [ ] `LIT-003` `[P1]` Length-aware, size-aware, and shortest-job-first scheduling.
- [ ] `LIT-004` `[P0]` Continuous batching, iteration-level scheduling, and preemption.
- [ ] `LIT-005` `[P1]` KV-cache management, paging, swapping, and admission control.
- [ ] `LIT-006` `[P2]` Multi-GPU and distributed inference scheduling.
- [ ] `LIT-007` `[P1]` Workload characterization and public LLM serving traces.
- [ ] `LIT-008` `[P1]` Evaluation methods for throughput, latency, fairness, and service-level objectives.

## Coding Problems

- [ ] `CODE-001` `[P1]` Define a common request-trace schema.
- [ ] `CODE-002` `[P1]` Build a reproducible benchmark runner with pinned configuration.
- [ ] `CODE-003` `[P1]` Implement metric collection for throughput and latency distributions.
- [ ] `CODE-004` `[P2]` Implement baseline FIFO and length-aware scheduling policies.
- [ ] `CODE-005` `[P1]` Add deterministic workload replay and random-seed control.
- [ ] `CODE-006` `[P1]` Validate timestamp boundaries and GPU utilization measurements.
- [ ] `CODE-007` `[P1]` Create analysis scripts that preserve raw results and provenance.

## Experimental Questions

- [ ] `EXPQ-001` `[P1]` How does output-length prediction error affect throughput and latency?
- [ ] `EXPQ-002` `[P2]` Which error distributions are most damaging to a length-aware scheduler?
- [ ] `EXPQ-003` `[P1]` When does length-aware scheduling cause starvation or unfairness?
- [ ] `EXPQ-004` `[P1]` How do workload arrival rate and length distribution change policy rankings?
- [ ] `EXPQ-005` `[P2]` How do model size, batch size, and available GPU memory affect results?
- [ ] `EXPQ-006` `[P2]` Are improvements preserved under realistic bursty workloads?
- [ ] `EXPQ-007` `[P1]` How close can practical methods approach an oracle with true output lengths?

## Deep Reasoning and Synthesis Questions

- [ ] `SYN-001` `[P1]` What is the strongest falsifiable hypothesis connecting output-length prediction to measurable serving improvements?
- [ ] `SYN-002` `[P1]` What oracle upper bound would reveal the maximum possible benefit of perfect length information?
- [ ] `SYN-003` `[P2]` How can a scheduling policy remain robust when predictions are biased, uncertain, or adversarially wrong?
- [ ] `SYN-004` `[P2]` Which existing scheduling theory results transfer to autoregressive inference, and which assumptions fail?
- [ ] `SYN-005` `[P1]` What experimental confounders could make a scheduling improvement appear larger than it is?
- [ ] `SYN-006` `[P2]` What combination of novelty, evidence, and systems contribution would make this work publication-worthy?
- [ ] `SYN-007` `[P2]` Which negative results would be scientifically valuable and how should they be designed for?
