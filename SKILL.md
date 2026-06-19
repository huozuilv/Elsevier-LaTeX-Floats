---
name: elsevier-latex-floats
description: Diagnose and fix figure/table float placement problems in Elsevier LaTeX manuscripts, especially `cas-dc` or `cas-sc` templates using pdfLaTeX on Overleaf. Use when figures or tables drift too far, create blank space/pages, fail to appear near discussion, misbehave in two-column layouts, or when editing `figure`, `figure*`, `table`, `table*`, `\FloatBarrier`, `[H]`, `[!htbp]`, `includegraphics`, captions, or float-related packages.
---

# Elsevier LaTeX Floats

## Operating Assumptions

- Treat Overleaf pdfLaTeX output as authoritative for Elsevier `cas-dc` and `cas-sc` manuscripts; local compilers may not reproduce placement exactly.
- Preserve project-specific instructions from `AGENTS.md`, `agent.md`, or nearby paper notes before applying this skill.
- Prefer local, minimal float adjustments over broad rewrites. Do not rewrite all floats to force placement.
- If Overleaf placement is stale or surprising, ask the user to try `Recompile from scratch` before making invasive changes.

## Diagnostic Workflow

1. Inspect the document class, compiler notes, float packages, and all float declarations:

```powershell
rg -n "documentclass|usepackage\{(placeins|float|flushend|stfloats|dblfloatfix)\}|FloatBarrier|begin\{figure|begin\{table|includegraphics|caption|label" "main.tex"
```

2. Identify the failure mode:
   - too much blank space before/after a float
   - single-column material incorrectly written as a double-column float
   - double-column material too tall for the page
   - too many forced placements blocking the float queue
   - `\FloatBarrier` inserted immediately after major sections
   - `[H]`, `[!htbp]`, or repeated `[!t]` used to make many floats appear immediately

3. Change the smallest local cause first:
   - reduce image height or width
   - shorten a long caption if the user permits content edits
   - move a float definition slightly earlier in the source
   - change single-column vs double-column environment
   - remove unnecessary barriers or excessive force specifiers

## Recommended Patterns

Use ordinary single-column figures for regular explanatory graphics:

```tex
\begin{figure}
    \centering
    \includegraphics[width=\linewidth]{...}
    \caption{...}
    \label{...}
\end{figure}
```

Use double-column figures only for wide graphics that need both columns:

```tex
\begin{figure*}
    \centering
    \includegraphics[width=0.95\linewidth]{...}
    \caption{...}
    \label{...}
\end{figure*}
```

For a double-column top float, use `[t]` sparingly:

```tex
\begin{figure*}[t]
```

Use tables according to their true width:

```tex
\begin{table}[t]
```

```tex
\begin{table*}[t]
```

For very tall double-column figures, cap height and preserve aspect ratio:

```tex
\includegraphics[width=\linewidth,height=0.56\textheight,keepaspectratio]{...}
```

## Avoid

- Do not insert `\FloatBarrier` immediately after Introduction, Method, or similar major sections just to force a nearby figure.
- Do not use `[H]` in Elsevier two-column templates unless the user explicitly accepts the layout risk.
- Do not convert many floats to `[!htbp]` or `[!t]`; this often makes the two-column float queue worse.
- Do not assume `placeins`, `float`, or `flushend` are harmful by themselves. The problem is usually barrier or placement overuse.
- Do not tune against final test/evaluation content when the manuscript is part of an experimental paper with anti-leakage rules.

## Validation

After editing, rerun the float inspection command and confirm:

- no unnecessary `\FloatBarrier` remains
- `[H]` is absent unless explicitly justified
- only selected top-priority double-column floats use `[t]`
- ordinary figures use `figure` and wide figures use `figure*`
- tall wide figures have a reasonable height cap

If a compiler is available, build the manuscript and inspect the rendered PDF pages around each changed float. If local compilation is unavailable or differs from Overleaf, report the limitation and give the user the exact Overleaf checks to perform.
