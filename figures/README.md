# Figures

The article references three figures (place the real PNGs here before publishing):

- `fig_architecture.png` — Figure 1: Chorus exposes seven seq2func oracles behind one
  interface + an agentic (MCP) layer.
- `fig_sort1_comparison.png` — Figure 2: rs12740374 multi-oracle table + single-axis
  genome-browser view, next to the Musunuru et al. (2010) experimental ground truth.
- `fig_crossoracle_browser.png` — Figure 3: cross-oracle consensus + shared genome-browser view.

These are not yet committed (the reproduction harness does not need them). They can be regenerated
from Chorus MCP outputs (the multi-oracle HTML reports under
`examples/walkthroughs/validation/SORT1_rs12740374_multioracle/` in the chorus repo) or supplied
by the authors. Figure 2's numbers should match `../reproduce/claims.yaml` (Analysis A), with the
corrected values (CAGE +1.52, BPNet CEBPB +2.5).
