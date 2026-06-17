# Figures

Three figures, all consistent with `../reproduce/claims.yaml` (Analysis A) and the article prose.

| File | Figure | How it is made | Numbers |
|---|---|---|---|
| `fig_architecture.png` | Fig 1 — Chorus's seven oracles behind one interface + an agentic (MCP) layer | Rendered from `fig1_architecture.html` (chorus house style); a polished alternative can be generated from `fig1_architecture_design_prompt.md` | windows/tool-count per `claims.yaml` F1/F3/F4 |
| `fig_sort1_comparison.png` | Fig 2 — rs12740374 per-layer predictions next to the Musunuru (2010) ground truth | Rendered from `fig2_sort1.html` (chorus house style) | Analysis-A claims (A1–A8) |
| `fig_crossoracle_browser.png` | Fig 3 — cross-oracle consensus + shared genome browser | Screenshot of the chorus multi-oracle report (pure chorus output) | Analysis-A claims |

## Regenerating

**Fig 1** is the one non-data figure. The committed `fig_architecture.png` is rendered from
`fig1_architecture.html` (same house style as Fig 2; all seven oracles incl. EPInformer-seq,
23-tool MCP layer). For a more polished version, run `fig1_architecture_design_prompt.md` through a
design tool and overwrite `fig_architecture.png`. (The original committed file was outdated — six
oracles, missing EPInformer-seq.)
```bash
python /path/to/shot.py figures/fig1_architecture.html figures/fig_architecture.png 1600 3
# then trim trailing whitespace (PIL ImageChops.difference → getbbox)
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
The consensus table reads ChromBPNet **+1.37**, LegNet **+0.30**, AlphaGenome CEBPA **+2.77** /
H3K27ac **+1.26** / CAGE **+1.52** / DNASE **+1.33** — the Analysis-A claims.

> Reproduction note: each oracle must be scored on its **native input window** centred on the variant.
> A 1 Mb locus tiles the short-input models (LegNet 200 bp, ChromBPNet 2,114 bp) into thousands of
> windows and averages the single-variant effect to ~0 (LegNet would read +0.000, ChromBPNet ~+0.68).
> `regenerate_multioracle.py` does this correctly; the genome-browser sub-panel renders via IGV.js,
> which needs Chrome with `--disable-web-security` to fetch the hg38 registry from a `file://` page.
