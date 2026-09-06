# Paper Reading Workflow

## Purpose

This workflow turns paper reading into traceable evidence for the literature map. It separates a paper's claims from this project's interpretation and prevents unread or partially read papers from being treated as established evidence.

## Workflow

1. **Queue:** Record a candidate only when its title, authors, year, and stable source can be verified.
2. **Triage:** Read the abstract, introduction, conclusion, and evaluation overview. Decide whether a close reading is relevant to a roadmap stage or backlog item.
3. **Close read:** Create `papers/notes/<paper-id>.md` only when close reading begins. Use a stable ID such as `P-YYYY-NNN`.
4. **Extract evidence:** Record the exact experimental setting, baselines, metrics, result location, assumptions, and limitations. Clearly label author claims and personal interpretation.
5. **Compare:** Add or update one row in `literature_matrix.md` and link its reading note.
6. **Integrate:** Link useful findings to a durable concept, system, or scheduling note. Add unresolved questions to `AI_BACKLOG.md` with the originating paper ID.
7. **Revisit:** Change a paper's status only when the corresponding level of review or reproduction has actually occurred.

## Reading Note Template

Copy the following headings into `papers/notes/<paper-id>.md` when a real paper enters close reading:

```markdown
# <Paper Title>

- Paper ID:
- Authors:
- Year / venue:
- Primary source / DOI:
- Code / data:
- Review status: skimmed | read | verified | reproduced
- Related backlog or roadmap item:

## Problem and Motivation

## Method

## System and Workload Assumptions

## Evaluation Setup

- Models:
- Hardware:
- Workloads / datasets:
- Baselines:
- Metrics:

## Main Claims and Supporting Evidence

For each important claim, record where the evidence appears and whether it was independently checked.

## Limitations and Threats to Validity

## Relevance to This Research

## Questions and Follow-up Actions
```

Do not store copyrighted paper PDFs in the repository unless redistribution is permitted. Prefer stable publisher, author, or archival links.
