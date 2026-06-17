# One-shot reproduction prompt

Copy everything in the box below and hand it to a fresh coding agent (Claude Code, or similar) — or
follow it yourself. It reproduces the **whole** article *as a reader would*: install Chorus, drive the
article's own conversational prompts through the MCP server, check each result against
`reproduce/claims.yaml`, optionally regenerate the figures, and emit a verdict. **No custom windows,
no special recipes, no hacks** — if a number only reproduces with a non-obvious trick, that is a
finding, not a workaround.

---

```
You are an independent reviewer reproducing the blog article in this repository
(article/2026-006_chorus.md). Do not trust the article — test it. Be honest: report the
exact number you get; mark blocked checks COULD-NOT-TEST (never FAIL) and say what was missing.

STEP 0 — Record run metadata.
  OS, CPU/GPU, `git rev-parse HEAD` of chorus, AlphaGenome backend (JAX `alphagenome` vs
  PyTorch `alphagenome_pt`), and which tokens you hold (HF_TOKEN, LDLINK_TOKEN).

STEP 1 — Install Chorus from main (the article's "Try it yourself" steps, verbatim).
  git clone https://github.com/pinellolab/chorus.git && cd chorus
  git rev-parse HEAD          # must be >= 9f21266 (PRs #91/#92/#93 merged)
  mamba env create -f environment.yml && mamba activate chorus && python -m pip install -e .
  chorus setup --oracle alphagenome     # JAX, gated — needs HF_TOKEN
  chorus setup --oracle chrombpnet      # TF; includes the BPNet CHIP heads
  chorus setup --oracle legnet          # PyTorch (MPRA)
  chorus health --timeout 300
  Tokens: export HF_TOKEN=hf_...  (accept the licence at google/alphagenome-all-folds)
          export LDLINK_TOKEN=... (free at ldlink.nih.gov — needed for Analysis B fine-mapping)
  Why >= 9f21266: PR #93 makes the conversational/1bp path return the correct magnitude for
  fixed-input oracles (ChromBPNet/Borzoi); PR #92 fixes the LD-proxy alleles + fine-map window
  Analysis B depends on. On an older commit, claims A1/A4 and all of Analysis B will NOT reproduce.

STEP 2 — Connect the MCP server (the article's primary, conversational mode).
  claude mcp add chorus -- mamba run -n chorus chorus-mcp
  Restart the agent so the server loads. Then DRIVE THE ARTICLE'S OWN PROMPTS — for each claim in
  reproduce/claims.yaml, send its `prompt` (a plain-language question) to the chorus tools, exactly
  as a reader following the article would. Do NOT pass empty assay_ids to AlphaGenome at 1 Mb (it is
  memory-guarded and will raise) — name the tracks, as the prompts do.

STEP 3 — Verify every claim.
  For each entry in reproduce/claims.yaml (Analysis A = SORT1/rs12740374, Analysis B =
  rs9504151/CDYL, plus the repo facts F1–F4):
    - Run its `prompt` through the MCP server (conversational — the primary check).
    - Optionally run its `python_recipe` for an exact deterministic number (cross-check only).
    - Compare to `expected`/`reproduced` within `tolerance`; apply `pass_if`.
    - Record PASS / FAIL / COULD-NOT-TEST and the value you got.
  Numbers are deterministic given weights + window + scoring window, so they should land inside the
  stated tolerances. Conventions: chromatin/TF/CAGE/histone effects are log2FC; MPRA is Δ(alt−ref);
  RNA is lnFC.

STEP 4 — (Optional) Regenerate the figures from Chorus and confirm they match the claims.
  Figure 2 (figures/fig_sort1_comparison.png) and Figure 3 (figures/fig_crossoracle_browser.png) are
  committed in this repo; their numbers must equal reproduce/claims.yaml (Analysis A). To regenerate
  from scratch instead of trusting them:
    # in the chorus repo (needs the multi-oracle native-window fix; see note below)
    mamba run -n chorus-legnet      python scripts/regenerate_multioracle.py --oracle legnet
    mamba run -n chorus-chrombpnet  python scripts/regenerate_multioracle.py --oracle chrombpnet
    mamba run -n chorus-alphagenome python scripts/regenerate_multioracle.py --oracle alphagenome
    mamba run -n chorus             python scripts/regenerate_multioracle.py --consolidate
  This writes examples/walkthroughs/validation/SORT1_rs12740374_multioracle/. The consensus table
  there must read ChromBPNet +1.37, LegNet +0.30, AlphaGenome CEBPA +2.77 / H3K27ac +1.26 /
  CAGE +1.52 / DNASE +1.33 — i.e. claims A1/A7/A3/A5/A6/A2. Figure 2's per-layer table and the
  "predictions vs ground truth" panel are rendered from figures/fig2_sort1.html, whose numbers are
  the same Analysis-A claims. (Rendering the report's IGV genome browser headless needs Chrome with
  --disable-web-security so the file:// page may fetch igv.org's hg38 registry; the data figures do not.)
  NOTE: scoring each oracle on its NATIVE input window (not a 1 Mb locus) is required — a long locus
  tiles the short-input models (LegNet 200 bp, ChromBPNet 2,114 bp) and averages the single-variant
  effect to ~0. regenerate_multioracle.py does this as of PR #94 (chorus main >= commit 336d64a);
  on an older checkout the consensus shows LegNet +0.000 / ChromBPNet ~+0.68 — update chorus.

STEP 5 — Output.
  A table: claim_id | expected | your_value | PASS/FAIL/COULD-NOT-TEST | note
  Then a one-line headline verdict (fully / mostly / partially reproduces) and your STEP-0 metadata.
  For any FAIL, quote the article sentence (or claims.yaml entry) and propose a corrected number.
  Do NOT edit the article to make a claim pass — report the discrepancy and let the authors decide.
```

---

## What "reproduced" should look like
On chorus `main` with the AlphaGenome, ChromBPNet/BPNet and LegNet oracles installed and an LDlink
token, every Analysis-A claim and the Analysis-B ranking/mechanism claims reproduce within tolerance;
the figure numbers equal the Analysis-A claims. Checks that need a token or model you do not have
(e.g. AlphaGenome without `HF_TOKEN`, or the IMR-90 lung-fibroblast ChromBPNet model) are
COULD-NOT-TEST, not failures. See `REPRODUCE.md` for the longer-form version of these steps and
`CLAUDE.md` for agent-specific guidance.
