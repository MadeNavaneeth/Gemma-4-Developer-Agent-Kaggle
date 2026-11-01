# Friends of the Port — `Gemma-4-Developer-Agent-Kaggle`

Working repository for the main **Google - The Gemma 4 Developer Agent**
competition (deadline **2026-12-02 23:59 UTC**). Internal codename: *Friends of
the Port*.

| Item | Value |
| --- | --- |
| Competition | `gemma-4-developer-agent` ($75,000) |
| Deadline | 2026-12-02 23:59 UTC |
| Hardware | Kaggle L4×4 (submission) / RTX 3070 Ti Laptop 8 GB (local) |
| Models | Gemma 4 `31b`, `12b`, `e4b`, `e2b` QAT, w4a16 |
| Time budget | 12 h total per task set |
| Submission | zip ≤ 3 GiB |

The paper-track companion repository lives at
`Gemma-4-Developer-Agent-Paper-Track-Kaggle` (deadline 2026-11-12). Shared
code (the `gemma4devpaper` package) ships with the paper repo; this repo holds
the agent run-book, prompts, and submission pipeline for the main track.

## Layout

```
src/gemma4devagent/   agent-side code shipped in submissions
scripts/              experiment + submission tooling
docs/                 competition facts, harness notes, run-book
tests/                smoke tests (python -m pytest -q)
```

## Quickstart

```bash
python -m pytest -q                      # smoke tests
python scripts/e0_audit.py --data ../Gemma-4-Developer-Agent-Paper-Track-Kaggle/data/raw
```

## Status

- [x] Competition joined (`userHasEntered: True`)
- [x] Dataset downloaded (graphs + embeddings + tasks)
- [x] Audit (E0) running — see sibling paper repo
- [ ] Harness skill / agent loop ported to submission format
- [ ] First scored dry run on Kaggle L4×4
