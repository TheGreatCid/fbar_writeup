# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Building

Compile the document with latexmk (shell-escape is required for pgfplots to read CSV data files):

```bash
latexmk -shell-escape -pdf -outdir=. main.tex
```

Clean build artifacts:

```bash
latexmk -C
```

The VS Code LaTeX Workshop extension is configured in `.vscode/settings.json` to pass `-shell-escape` automatically.

## Project structure

This is a LaTeX writeup comparing two methods for mapping the F-bar (volumetric locking) deformation gradient across a mesh restart in a parallel FEM simulation.

- `main.tex` — the single source document; preamble, all sections, and figure includes live here
- `figures/` — pgfplots figure files, included via `\input{figures/<name>}` from `main.tex`
- `data/` — CSV output from FEM runs, read directly by pgfplots at compile time (no preprocessing)
- `tikz/` — standalone TikZ scratch files (not included in main document)

## Data conventions

CSV files follow the naming pattern `{series}_fbar_out_{n}.csv` where `series` is either `reference` or `restart` and `n` is the processor count (1, 2, 4, 8).

Columns: `time`, `ep_int`, `psie_corr_active_int`, `psip_active_int`

- Reference files span `t ∈ [0, 1.5]`
- Restart files span `t ∈ [1.5, 2.5]` but their **first row is always `(1.5, 0, 0, 0)`** — an initialization artifact. Filter it out when plotting (e.g. `x filter/.code={\pgfmathparse{#1<=1.5?nan:#1}}`).

## pgfplots figures

Data paths in figure files are relative to `main.tex` (the compilation root), not to the figure file itself. A figure at `figures/foo.tex` must reference `data/bar.csv`, not `../data/bar.csv`.

The `\hl{...}` command in `main.tex` highlights terms in red boxes — used to annotate which quantity is mapped/modified in each method.
