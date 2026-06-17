# Figures

Three figures, all consistent with `../reproduce/claims.yaml` (Analysis A) and the article prose.

| File | Figure | How it is made | Numbers |
|---|---|---|---|
| `fig_architecture.png` | Fig 1 — Chorus's seven oracles behind one interface + an agentic (MCP) layer | Authors' high-quality export from the Claude Design project; `fig_architecture.html` + `logo_burst.png`/`logo_wordmark.png` are the near-identical editable source | windows/tool-count per `claims.yaml` F1/F3/F4 |
| `fig_sort1_comparison.png` | Fig 2 — rs12740374 per-layer predictions next to the Musunuru (2010) ground truth | Rendered from `fig2_sort1.html` (chorus house style) | Analysis-A claims (A1–A8) |
| `fig_crossoracle_browser.png` | Fig 3 — cross-oracle consensus + shared genome browser | Screenshot of the chorus multi-oracle report (pure chorus output) | Analysis-A claims |

## Regenerating

**Fig 1** is the one non-data figure — a Chorus-branded architecture diagram (logo lockup, agentic
MCP layer with 23 tools, all seven oracles incl. EPInformer-seq with their input windows, a context
ruler, and the outcome tiles). It was authored in a Claude Design project
(`https://claude.ai/design/p/c23284e8-727f-4de2-97be-61a15b88c2ae`) and flattened to a standalone,
self-contained `fig_architecture.html` (fixed 1600×1080 canvas; assets `logo_burst.png` +
`logo_wordmark.png`) so it renders without the design runtime:
```bash
python /path/to/shot.py figures/fig_architecture.html figures/fig_full.png 1600 6
# then crop the top 1600×1080 canvas (3200×2160 @2x): PIL Image.crop((0,0,3200,2160))
```

**Fig 2** — edit/keep `fig2_sort1.html`, then:
```bash
# from the chorus repo (selenium + Chrome)
python /path/to/shot.py figures/fig2_sort1.html figures/fig_sort1_comparison.png 1200 3
```
The HTML carries every number explicitly; each is an Analysis-A claim, so the figure cannot drift
from `claims.yaml`. The "predictions vs ground truth" panels were always an HTML+CSS composition
(not a chorus report function) — `fig2_sort1.html` is that source, committed here so it is editable
and reproducible.

**Fig 3** is pure chorus output. Regenerate the multi-oracle report, then screenshot it:
```bash
# in chorus (each oracle in its env), then consolidate
mamba run -n chorus-legnet      python scripts/regenerate_multioracle.py --oracle legnet
mamba run -n chorus-chrombpnet  python scripts/regenerate_multioracle.py --oracle chrombpnet
mamba run -n chorus-alphagenome python scripts/regenerate_multioracle.py --oracle alphagenome
mamba run -n chorus             python scripts/regenerate_multioracle.py --consolidate
# screenshot examples/walkthroughs/validation/SORT1_rs12740374_multioracle/rs12740374_SORT1_multioracle_report.html
```
The consensus table shows the freshly **reproduced** values (claims.yaml `reproduced`) — ChromBPNet
**+1.37**, LegNet **+0.35**, AlphaGenome CEBPA **+2.77** / H3K27ac **+1.26** / CAGE **+1.52** /
DNASE **+1.33**. These match the article's reported Analysis-A numbers within rounding (e.g. H3K27ac
1.264 vs the article's +1.27; LegNet's single-window +0.347 vs the +0.30 the article reports).

> Reproduction note: each oracle must be scored on its **native input window** centred on the variant.
> A 1 Mb locus tiles the short-input models (LegNet 200 bp, ChromBPNet 2,114 bp) into thousands of
> windows and averages the single-variant effect to ~0 (LegNet would read +0.000, ChromBPNet ~+0.68).
> `regenerate_multioracle.py` does this correctly as of PR #94 (chorus `main` ≥ commit `336d64a`);
> the genome-browser sub-panel renders via IGV.js, which needs Chrome with `--disable-web-security`
> to fetch the hg38 registry from a `file://` page.
