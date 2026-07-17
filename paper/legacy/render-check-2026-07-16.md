---
title: DLM Mermaid Render Check 2026-07-16
domain: research
area: dlm
type: checklist
status: stable
updated: 2026-07-16
tags: [render-check, mermaid, visualization, dlm]
---

# DLM Mermaid Render Check 2026-07-16

返回 [[research/dlm/paper/legacy/index]]。

## Result

Chrome headless rendered all Mermaid diagrams in `wiki/research/dlm/` successfully.

| Metric | Value |
|---|---:|
| Mermaid diagrams found | 12 |
| Render failures | 0 |
| Screenshot | [[research/dlm/paper/legacy/render-check-2026-07-16.png]] |

## Diagrams Checked

| ID | Source |
|---|---|
| d1 | [[research/dlm/paper/legacy/diffusion-process-visual]] line 15 |
| d2 | [[research/dlm/paper/legacy/diffusion-process-visual]] line 37 |
| d3 | [[research/dlm/paper/legacy/diffusion-process-visual]] line 52 |
| d4 | [[research/dlm/paper/legacy/diffusion-process-visual]] line 82 |
| d5 | [[research/dlm/paper/legacy/history-and-landscape]] line 15 |
| d6 | [[research/dlm/paper/legacy/history-and-landscape]] line 40 |
| d7 | [[research/dlm/paper/legacy/index]] line 24 |
| d8 | [[research/dlm/paper/legacy/math-or-discussion]] line 40 |
| d9 | [[research/dlm/paper/legacy/math-or-discussion]] line 88 |
| d10 | [[research/dlm/paper/legacy/ideas/index]] line 27 |
| d11 | [[research/dlm/index]] line 17 |
| d12 | [[research/dlm/reference/index]] line 27 |

## Method

The check generated a temporary HTML page containing the extracted Mermaid blocks, loaded Mermaid 11 from CDN, and rendered the page with system Google Chrome in headless mode. The DOM summary returned:

```json
{"total":12,"failed":0}
```

The macOS headless run emitted CVDisplayLink and Google service log noise, but the browser completed rendering and wrote the screenshot artifact above.
