# Figures

All figures are consistent with `../reproduce/claims.yaml` and the article prose, in reading order.

| File | Figure | How it is made | Numbers |
|---|---|---|---|
| `fig_architecture.png` | Fig 1 — Chorus's seven oracles behind one interface + an agentic (MCP) layer | Authors' high-quality export from the Claude Design project; `fig_architecture.html` + `logo_burst.png`/`logo_wordmark.png` are the near-identical editable source | windows/tool-count per `claims.yaml` F1/F3/F4 |
| `fig_sort1_tf_scan.png` | Fig 2 — SORT1 TF-ChIP scan (no prior; C/EBP family tops) | `regenerate_analysis_figures.py tf_scan` from an AlphaGenome `discover_variant_effects` run (HepG2) | claims A3/A9 (CEBPB +3.1, CEBPA +2.8) |
| `fig_sort1_comparison.png` | Fig 3 — rs12740374 per-layer predictions next to the Musunuru (2010) ground truth | Rendered from `fig2_sort1.html` (chorus house style) | Analysis-A claims (A1–A8) |
| `fig_sort1_browser.png` | Fig 4 — every oracle's signal on one normalised genome-browser axis across the SORT1 locus | Screenshot of the chorus multi-oracle report's IGV browser, zoomed to chr1:109.15–109.45 Mb (pure chorus output) | the per-track signals behind Analysis A |
| `fig_rs9504151_finemap.png` | Fig 5 — rs9504151 causal-variant prioritisation (Top-N table) | `regenerate_analysis_figures.py finemap` from `fetch_ld_variants` + `prioritize_causal_variants` (AlphaGenome lung-fibroblast tracks, 56 CEU SNV proxies) | claims B2 (rs9504151 #1; rs62384944 → rank 4) |
| `fig_rs9504151_ism.png` | Fig 6 — rs9504151 multi-oracle ISM motif logos (converge on ATF4) | `regenerate_analysis_figures.py ism` from `score_ism`/`saturation_mutagenesis` on LegNet + AlphaGenome + ChromBPNet | claims B6 (ATF4 motif TGATGCAA) |

**Analysis panels (Figs 2, 5, 6)** are regenerated from Chorus by `scripts/regenerate_analysis_figures.py` (in the chorus repo) reading the JSON outputs of `discover_variant_effects` (TF scan), `prioritize_causal_variants` (fine-map), and the `saturation_mutagenesis` / `score_ism` (ISM logos via logomaker). The ISM logos are model-independent evidence: three oracles trained on different assays all converge on the same ATF4 motif.

## Regenerating

**Fig 1** is the one non-data figure — a Chorus-branded architecture diagram (logo lockup, agentic
MCP layer with 24 tools, all seven oracles incl. EPInformer-seq with their input windows, a context
ruler, the outcome tiles). Authored in a Claude Design project
(`https://claude.ai/design/p/c23284e8-727f-4de2-97be-61a15b88c2ae`) and flattened to a standalone,
self-contained `fig_architecture.html` (fixed 1600×1080; assets `logo_burst.png` + `logo_wordmark.png`):
```bash
python /path/to/shot.py figures/fig_architecture.html figures/fig_full.png 1600 6
# then crop the top 1600×1080 canvas (3200×2160 @2x): PIL Image.crop((0,0,3200,2160))
```

**Fig 3** — edit/keep `fig2_sort1.html`, then `python /path/to/shot.py figures/fig2_sort1.html
figures/fig_sort1_comparison.png 1200 3`. The HTML carries every number explicitly; each is an
Analysis-A claim, so the figure cannot drift from `claims.yaml`. The "predictions vs ground truth"
panels were always an HTML+CSS composition (not a chorus report function) — `fig2_sort1.html` is that
source, committed here so it is editable and reproducible.

**Fig 4 (multitrack browser)** is pure chorus output — the IGV browser from the multi-oracle report.
Regenerate the report, then screenshot just the browser:
```bash
# in chorus (each oracle in its env), then consolidate
mamba run -n chorus-legnet      python scripts/regenerate_multioracle.py --oracle legnet
mamba run -n chorus-chrombpnet  python scripts/regenerate_multioracle.py --oracle chrombpnet
mamba run -n chorus-alphagenome python scripts/regenerate_multioracle.py --oracle alphagenome
mamba run -n chorus             python scripts/regenerate_multioracle.py --consolidate
# set the report's IGV "locus" to chr1:109150000-109450000, screenshot the genome-browser section,
# and crop to the browser. The consensus-table numbers read ChromBPNet +1.37, LegNet +0.35,
# AlphaGenome CEBPA +2.77 / H3K27ac +1.26 / CAGE +1.52 / DNASE +1.33 (claims.yaml `reproduced`).
```

> Reproduction notes: (1) each oracle must be scored on its **native input window** centred on the
> variant — a 1 Mb locus tiles the short-input models (LegNet 200 bp, ChromBPNet 2,114 bp) and averages
> the single-variant effect to ~0; `regenerate_multioracle.py` does this as of PR #94. (2) the IGV
> browser renders via IGV.js, which needs Chrome with `--disable-web-security` so a `file://` page can
> fetch the hg38 registry. (3) the conversational fine-map / multilayer path needs PR #97.
