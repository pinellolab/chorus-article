# Figure 1 — design brief (paste into Claude design / an image-generation design tool)

This is a **design brief** for the article's Figure 1 (architecture diagram). It is not auto-generated
from Chorus; an author runs this prompt through a design tool, then drops the result in as
`fig_architecture.png`. The numbers and oracle list below are the source of truth — keep them exact.
They must match the article's oracle table (`article/2026-006_chorus.md`) and `reproduce/claims.yaml`
(facts `F1`, `F4`). The current `fig_architecture.png` is **outdated** (only six oracles; missing
EPInformer-seq) — this brief replaces it.

---

## Paste-ready prompt

> Design a clean, modern **scientific architecture diagram** for a software framework called
> **Chorus**, in landscape format (target **16:9**, ~1600×900). It should read top-to-bottom as a
> three-layer stack. Use a light background (white / very light slate), a calm palette of slate grey
> with a single blue accent (#2B81AF) plus soft greens/ambers for category tags. Typography:
> a clean humanist sans-serif (Open Sans / Inter). Flat, professional, no skeuomorphism, no drop
> shadows beyond subtle elevation. Every label must be legible.
>
> **Title band (top):** large wordmark **“Chorus”**, with the tagline
> *“An agentic framework for orchestrating sequence-to-function genomic oracles”* and a thin
> sub-line *“From natural language to multi-model regulatory analysis.”*
>
> **Layer 1 — the agentic / MCP orchestration layer (a prominent banner just under the title).**
> Label it **“Agentic AI orchestration (MCP server — 23 tools)”**. Show four capability chips inside:
> *“Natural language → genomic analysis”*, *“Autonomous multi-step workflows”*,
> *“Cross-model comparison & reasoning”*, *“Interactive genome-browser reports.”*
> Convey that a user/agent talks to this layer in plain language and it drives everything below.
> A small ground-level note: *“Unified Python API · per-track normalisation · reproducible per-oracle
> environments.”*
>
> **Layer 2 — the seven oracles (the visual centre of the figure).** A row (or two balanced rows) of
> **seven** equally-weighted cards, one per oracle, each showing the oracle name, its **input window**,
> and a one-line capability. Order them by input window, short → long, so the range is visually obvious.
> Use exactly these seven (do **not** invent or omit any; EPInformer-seq must be present):
>
> | Oracle | Input window | What it does |
> |---|---|---|
> | **LegNet** | 200 bp | MPRA / reporter activity of short regulatory elements |
> | **ChromBPNet / BPNet** | 2,114 bp | Base-pair chromatin accessibility (ChromBPNet) + TF binding (BPNet) |
> | **EPInformer-seq** | 2,114 bp | Per-cell enhancer activity (DNase cut-sites + H3K27ac), 11 Roadmap cell types |
> | **Sei** | 4,096 bp | Regulatory effect across 21,907 chromatin profiles |
> | **Enformer** | 393,216 bp | Expression (CAGE), accessibility & histone marks over long context |
> | **Borzoi** | 524,288 bp | Enformer-style outputs plus RNA-seq coverage |
> | **AlphaGenome** | 1,048,576 bp | Generalist: ATAC, DNase, CAGE, RNA-seq, splicing, PRO-CAP, TF/histone ChIP (5,731 tracks) |
>
> Visually hint the window scale, e.g. a thin horizontal “context ruler” under the row growing from
> 200 bp on the left to ~1 Mb on the right, so a reader instantly sees these span ~5 orders of magnitude.
>
> **Layer 3 — what you get out (a thin footer row of outcome tiles).** Five tiles:
> *“Multi-layer variant-effect analysis”*, *“Causal-variant fine-mapping”*,
> *“Cross-oracle consensus & normalised genome browser”*, *“In-silico mutagenesis & sequence design”*,
> *“High-throughput VCF / GWAS screening.”*
>
> **Flow:** show a clear vertical flow — natural-language question enters the agentic layer (top), which
> selects and calls the right oracle(s) (middle), producing comparable, normalised results (bottom).
> Thin connector lines, arrowheads pointing downward, no clutter. Keep it elegant and uncluttered;
> the seven-oracle row is the hero.

---

## Constraints / checklist for whoever generates this
- **Seven** oracles, exactly as in the table above — EPInformer-seq **must** appear (the old figure omitted it).
- Windows must read exactly: 200 bp · 2,114 bp · 2,114 bp · 4,096 bp · 393,216 bp · 524,288 bp · 1,048,576 bp.
- “23 tools” on the MCP layer (matches `claims.yaml` F3 and the article).
- Landscape, ~16:9, legible at article width (~1600 px wide).
- Palette: slate + #2B81AF blue accent; matches the Figure 2 / Figure 3 house style.
- Save the result as `figures/fig_architecture.png` (replace the outdated file).
