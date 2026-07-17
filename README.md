# DLM Research Base

A shared, background-first research base for diffusion language models, discrete
diffusion, flow matching, and optimization-aware decoding. The repository does
not commit to a new algorithmic direction yet; it establishes the mathematical
and experimental foundation needed for team discussion.

## Start Here

Read [`paper/main.pdf`](paper/main.pdf). It is a compact ICML-style discussion
paper covering the diffusion process, model and sampler interfaces, the main
literature branches, and the evaluation contract for later algorithm design.

## Structure

- [`reference/`](reference/): literature maps, reading order, search provenance,
  locally retained papers, and extracted text for search.
- [`paper/`](paper/): the active English paper, TikZ figures, bibliography, and
  archived idea provenance.

The detailed wiki entry is [`index.md`](index.md).

## Build

From `paper/`, run:

```bash
latexmk -pdf -interaction=nonstopmode -halt-on-error main.tex
```

Use `latexmk -c main.tex` to remove intermediate build files. The compiled
`main.pdf` is tracked so collaborators can start reading without a TeX setup.

## Sharing

This repository is private because `reference/papers/` contains locally retained
paper PDFs. Give collaborators repository access instead of republishing the
PDF collection. A public release should retain the notes, BibTeX records, and
canonical URLs while excluding copyrighted full texts.
