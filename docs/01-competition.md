# Competition facts — main track

Source: Kaggle `gemma-4-developer-agent` page + `HARNESS_README.md`
(deadline **2026-12-02 23:59 UTC**, $75,000, featured).

These are working notes cross-checked against the downloaded bundle; treat the
Kaggle page as authoritative where they disagree.

## Task set

- SWE-bench-style: fix a real GitHub issue from the issue text alone.
- Public development set: **129 tasks** in 4 repos
  (`fastapi/fastapi` 67, `Textualize/rich` 48, `psf/requests` 13,
  `encode/httpx` 1); hidden evaluation set drawn from private repos.
- `tasks.jsonl` fields: `instance_id, repo, base_commit, problem_statement,
  hints_text, patch, test_patch, created_at` — the `patch` is present for
  public tasks only (used for analysis, never shipped to the agent).

## Harness

Tools: `run_command`, `submit_patch`, `get_status`, `read_file`, `edit_file`,
`write_file`, `get_code_neighbors`, `search_similar_code`, `get_code_subgraph`.
Budget: 12 h total. Submission zip ≤ 3 GiB.

**Retrieval routing (verified from `swegemma` 0.2.11 source):**

- `search_similar_code` passes the query as a *node name*;
  `embedding_utils.embed` is a dictionary lookup, not a model — a sentence
  misses the cache and the suffix resolver, and the tool falls back to a
  hand-written lexical scorer (`+3` name substring, `+1` text substring,
  `+0.5` non-test, normalised by `4·|tokens|+0.5`).
- `get_code_neighbors` / `get_code_subgraph` resolve the same way and return
  `[]` for a sentence.
- Consequence: the agent's issue text never reaches the 256-d embedding space
  unless the prompt first resolves an identifier.

## Data artifacts (commit-keyed)

- `graphs/<repo>_<commit>.json` — NetworkX multigraph JSON,
  `nodes[*]{id,name,text}`, `edges[*]{source,target,type,key}`;
  `name == id` on the sampled fastapi commits (~4,287 nodes / 2,490 edges;
  ~76% of nodes are `tests.*` — verify per repo in the audit before relying
  on it).
- `embeddings/<repo>_<commit>.npz` — `float32[256]` per node, keys == node ids.
- `snapshots/<instance_id>.tgz` — repo-at-commit archives (~20 GB; optional).

## Constraints / hazards

- Kaggle notebooks run with no internet: anything shipped must be inside the
  3 GiB zip.
- `search_similar_code` similarity scores are not comparable between the
  embedding path (cosine) and the fallback (lexical fraction).
- Hidden set uses private repos: learned components must transfer across repos
  (evaluate leave-one-repo-out on the public four).
