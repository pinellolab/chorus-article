# Chorus article — "Chatting with genomic oracles" + reproduction harness

This repository holds the **Genomics×AI blog post 2026-006, "Chorus: chatting with genomic
oracles"** and everything an independent agent (or person) needs to **reproduce every
quantitative claim** in it from scratch.

The goal: a fresh agent reads the article, installs [Chorus](https://github.com/pinellolab/chorus),
runs the prompts/recipes, and checks each claim against an expected value + tolerance — then
reports PASS / FAIL / COULD-NOT-TEST. If it all passes, the article is reproducible end-to-end.

## Layout

| Path | What |
|---|---|
| `article/2026-006_chorus.md` | The article, in blog-publishable format (Hugo frontmatter + shortcodes). Source of truth for the prose and the prompts. |
| `reproduce/PROMPT.md` | **The one-shot reproduction prompt** — copy it to a fresh agent (or follow it yourself) to reproduce the whole package: install, run the article's prompts, verify each claim, regenerate the figures, report a verdict. Start here. |
| `reproduce/claims.yaml` | **Machine-actionable manifest** — one entry per claim: the conversational prompt, a deterministic Python recipe, the expected value, the tolerance, and the PASS criterion. |
| `reproduce/REPRODUCE.md` | Long-form version of the prompt: install, tokens, MCP, then run each claim and compare. |
| `reproduce/CLAUDE.md` | Drop-in guidance so an agent run in this repo knows its job. |
| `figures/` | The three article figures + their sources. Fig 2/Fig 3 are regenerated from Chorus (numbers match `claims.yaml`); Fig 1 ships as a design brief. See `figures/README.md`. |

## Reproducibility status (2026-06-17)

Every claim in the article reproduces against **Chorus `main`** at/after commit `9f21266`
(PRs #91 install fixes, #92 fine-mapping/LD fixes, #93 variant-scoring window fix — all merged
2026-06-17). Earlier commits will **not** reproduce the fixed-input-oracle magnitudes (ChromBPNet,
Borzoi) via the conversational path — install `main` (or pin ≥ `9f21266`).

Provenance of the expected values: an independent reproduction review (see
[chorus/audits/2026-06-16_blogpost_reproduction_report.md](https://github.com/pinellolab/chorus/blob/main/audits/2026-06-16_blogpost_reproduction_report.md)).

## Quick start (verifying agent)

Hand `reproduce/PROMPT.md` to a fresh agent — it is self-contained. Or do it by hand:

```bash
# 1. Install chorus (main has all reproducibility fixes)
git clone https://github.com/pinellolab/chorus.git && cd chorus
mamba env create -f environment.yml && mamba activate chorus && pip install -e .
chorus setup --oracle alphagenome --oracle chrombpnet --oracle legnet   # + tokens (see PROMPT.md)
# 2. Reproduce
#    Follow reproduce/PROMPT.md (or REPRODUCE.md); for each entry in reproduce/claims.yaml,
#    run the prompt (or python_recipe) and compare to expected ± tolerance.
```

## Tokens
- `HF_TOKEN` — required (AlphaGenome is gated). Accept the licence at `google/alphagenome-all-folds`.
- `LDLINK_TOKEN` — required for Analysis B (`fine_map_causal_variant` LD fetch). Free at ldlink.nih.gov.

## License
Article text: CC-BY (matching the blog). Code references: Chorus is MIT.
