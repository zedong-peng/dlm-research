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

- [`reference/index.md`](reference/index.md): literature maps, reading order, search provenance,
  locally retained papers, and extracted text for search.
- [`paper/index.md`](paper/index.md): the active English paper, TikZ figures, bibliography, and
  archived idea provenance.
- [`reference/papers/index.md`](reference/papers/index.md): the `assets/` archive
  catalog and linked reading notes or `threads/` unread records.
- `reportfrom-wang/`: locally retained collaborator materials:
  [document](<reportfrom-wang/CIR_Diffusion Doc.pdf>) and
  [slides](<reportfrom-wang/CIR_Diffusion Slide.pdf>). These have no reading record
  or canonical source metadata in this repository.

The detailed wiki entry is [`index.md`](index.md).

This is an independent research project used as the parent wiki's `dlm`
submodule. `paper/` retains the active research project; `reference/` retains
literature maps and provenance. Paper archives follow the parent wiki's
`assets/<slug>/citation.bib`, `paper-pdf/`, and evidence-based `note.md` layout;
sources without explicit reading records are linked from `threads/`. For wiki queries, start
with the reference maps, then consult the linked local PDF/text pairs; a retained
file alone does not establish that it has been read. Wiki-style links use the
parent wiki's `research/dlm/` namespace.

## Build

From `paper/`, run:

```bash
latexmk -pdf -interaction=nonstopmode -halt-on-error main.tex
```

Use `latexmk -c main.tex` to remove intermediate build files. The compiled
`main.pdf` is tracked so collaborators can start reading without a TeX setup.

## Sharing

This repository is private because `assets/` contains locally retained
paper PDFs. Give collaborators repository access instead of republishing the
PDF collection. A public release should retain the notes, BibTeX records, and
canonical URLs while excluding copyrighted full texts.
