# Reproducing the article — instructions for a verifying agent

You are an independent reviewer. Your job: **install Chorus, re-run every claim in
`article/2026-006_chorus.md`, and check each against `reproduce/claims.yaml`** (expected value
± tolerance). Report PASS / FAIL / COULD-NOT-TEST per claim with the number you got. Do **not**
trust the article; test it.

## 0. Record run metadata
OS, CPU/GPU, `git rev-parse HEAD` of chorus, AlphaGenome backend (JAX `alphagenome` vs PyTorch
`alphagenome_pt`), and which tokens you have. Numbers are deterministic given weights + window +
scoring window, so reproduced values should be close (see tolerances in `claims.yaml`).

## 1. Install Chorus (must be `main` ≥ commit 9f21266)
```bash
git clone https://github.com/pinellolab/chorus.git && cd chorus
git rev-parse HEAD          # confirm >= 9f21266 (PRs #91/#92/#93 merged)
mamba env create -f environment.yml && mamba activate chorus && python -m pip install -e .
chorus setup --oracle alphagenome     # JAX, gated — needs HF_TOKEN
chorus setup --oracle chrombpnet      # TF; includes BPNet (CHIP)
chorus setup --oracle legnet          # PyTorch (MPRA)
chorus health --timeout 300
```
**Tokens:** `export HF_TOKEN=hf_...` (accept the licence at `google/alphagenome-all-folds`);
`export LDLINK_TOKEN=...` (free at ldlink.nih.gov — required for Analysis B fine-mapping).

> Why ≥ 9f21266: PR #93 fixes a variant-scoring window bug that otherwise makes fixed-input
> oracles (ChromBPNet/Borzoi) under-report by ~4× on the conversational path. PR #92 fixes the
> LD-proxy alleles + fine-map window that Analysis B depends on. On an older commit, claims
> A1/A4 and all of Analysis B will NOT reproduce.

## 2. Connect the MCP server (conversational path — the article's primary mode)
```bash
claude mcp add chorus -- mamba run -n chorus chorus-mcp
```
Then drive the prompts in `claims.yaml`. After upgrading chorus, **restart Claude Code** so the
MCP server reloads. **Do not** call `analyze_variant_multilayer`/`discover_variant` with empty
`assay_ids` on AlphaGenome at 1 Mb — it is memory-guarded (PR #92) and will raise rather than
OOM; pass specific tracks.

## 3. Reproduce each claim
For every entry in `reproduce/claims.yaml`:
1. Run its `prompt` through the MCP server (conversational), **or** its `python_recipe` for an
   exact, deterministic number.
2. Compare to `expected`/`reproduced` within `tolerance`.
3. Apply `pass_if`. Record PASS / FAIL / COULD-NOT-TEST + your value.

Mark **COULD-NOT-TEST** (never "FAIL") when blocked by a missing token, missing cell-type model,
no GPU, etc. — and say what's needed.

## 4. Determinism notes
- AlphaGenome: identical numbers on JAX or PyTorch backend within ~1–2% fp32 noise.
- ChromBPNet/BPNet: deterministic; CPU and MPS agree.
- Fine-mapping (Analysis B) needs `LDLINK_TOKEN`; the credible set is the 1000G **CEU** r²>0.8
  LDproxy set (~54 variants). Ranking claims have ±1–2 rank tolerance.

## 5. Output
A table: `claim_id | expected | your_value | PASS/FAIL/COULD-NOT-TEST | note`, plus a one-line
headline verdict (fully / mostly / partially reproduces) and your run metadata. For any FAIL,
quote the article sentence/cell and propose a corrected number.
