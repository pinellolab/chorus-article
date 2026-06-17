# Agent guidance for this repo

If you are an agent working in this repository, your task is **reproduction verification** of the
blog post `article/2026-006_chorus.md`.

1. Read `reproduce/REPRODUCE.md` and follow it.
2. Treat `reproduce/claims.yaml` as the checklist. Reproduce each claim with the chorus MCP
   server (the `prompt`) and/or the `python_recipe`; compare to `expected` ± `tolerance`.
3. Install chorus from `main` (≥ commit `9f21266`). Confirm the commit before trusting magnitudes.
4. Be honest: mark blocked claims COULD-NOT-TEST, not FAIL. Report the exact number you got.
5. Produce the PASS/FAIL table and a headline verdict (see REPRODUCE.md §5).

Do not edit the article to make claims pass — if a claim does not reproduce, report it and
propose a corrected value for the authors.
