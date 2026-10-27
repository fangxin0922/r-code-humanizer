---
name: r-code-humanizer
description: Rewrite user-provided R code in the practical research-script style of the user's examples, with natural comments, clear sections, and straightforward code while preserving analysis behavior. Use when asked to humanize R code or match this R coding style.
---

# R Code Humanizer

Make supplied R code read like a working researcher's analysis script. The style reference is the user's examples of binning hospital diagnoses and standardizing and merging HCUP data. Apply their readable structure and practical tone, not their particular disease definitions, file paths, or incidental mistakes.

## General decision rules

These are preferences inferred from two distinct scripts, one of which was pasted twice. They describe this user's preferred research-code style, not a universal test of human authorship. The user's explicit quiet-output preference overrides the examples' messages and timers.

- Organize code around the research workflow: inputs, domain definitions, transformations, and outputs. Let sections correspond to real tasks, not a fixed template.
- Keep scientific choices visible. Named vectors, lookup tables, and predicates should make definitions easy to inspect and revise; do not bury them inside generic machinery.
- Match abstraction to repetition and meaning. Extract a coherent repeated task, but allow separate blocks when they express different scientific or data-handling decisions. Neither maximum reuse nor minimum line count is the objective.
- Choose names and comments for the reader's current context. Brief local names are fine; names that cross sections should reveal what the object represents. Spend comment detail on definitions, exceptions, and consequential choices.
- Use the simplest familiar R expression that makes the operation clear. Mix ordinary loops, pipes, vectorized expressions, and package idioms as appropriate; no single syntax must dominate.
- Prefer edits with a clear readability benefit. Keep sound existing code even if it differs from an incidental pattern in the examples. Do not add infrastructure, package installation, environment resets, or generic error-handling layers merely to make a script look complete.

For the reasoning behind these rules, borderline cases, and examples of how to apply them, read [references/style-reasoning.md](references/style-reasoning.md) when deciding on structural edits or explaining the style. The rules below are sufficient for straightforward cleanup.

## Preserve the analysis

Treat humanization as a style edit unless the user also requests substantive changes. Preserve input and output paths, filenames, exported column names, file formats, compression settings, data types, missing-value handling, grouping, filters, join keys and join behavior, row ordering, model specifications, reference levels, seeds, and numerical results.

Keep existing package choices and computational strategy where changing them could affect behavior or performance. In particular, preserve data.table reference semantics and the distinction between vectorized and scalar conditions. Do not replace a functioning dplyr workflow with data.table just because the examples use data.table. Do not add parallelism or change random-number generation as a style improvement.

If a likely bug or methodological issue appears, identify it separately rather than silently fixing it during a style rewrite. Change it when that is also within the user's requested scope, and explain the effect.

## Match the reference style

- Open a standalone script with a brief purpose comment. Retain an existing Rscript shebang when applicable. Preserve relevant source attribution; do not invent authors, contact information, provenance, or copy those details from the examples into unrelated scripts.
- Put library calls near the top, followed by paths and configuration. Use clear uppercase names for script-level constants when introducing or safely renaming them, such as `INPUT_DIR` and `OUTPUT_DIR`. Keep local objects and functions in readable snake_case; short names such as `dt`, `path`, and `state` work when their role is obvious.
- Use simple RStudio-style headings such as `# Variables ----`, `# Functions ----`, and `# Main ----`, with `## Read and merge parts ----` for useful subsections. Choose headings that fit the actual work; do not force every snippet into a full script template.
- Write short, concrete comments explaining variable meanings, domain rules, reasons for a step, or non-obvious transformations. Examples of the tone are `# DX1 = principal diagnosis`, `# Read and merge separate ZIP files if available`, and `# Write out`. Remove verbose narration, generic assurances, repetitive labels, and comments that merely restate obvious syntax. Retain explanations needed to understand the science.
- Group long vectors and lookup tables by meaning, with brief comments and line breaks. Keep real variable names, code lists, and mappings intact. Use consistent spacing and modest indentation; do not manufacture typos, uneven formatting, or awkward syntax to appear human.
- Prefer a readable sequence of operations, ordinary loops, and small functions with clear purposes. Avoid adding wrapper frameworks, configuration systems, or helpers used only to hide a simple statement. Keep existing useful abstraction and necessary input checks.
- Use native `|>` for clear transformations when compatible with the script's required R version and semantics. Preserve existing pipes when converting them offers little benefit or risks changing evaluation. Split dense operations into named intermediate objects when that makes the logic easier to follow.
- In data.table code, keep familiar idioms such as `:=`, column selection, and `rbindlist()` readable. Keep `file.path()` and `sprintf()` where they clarify file construction. Explicit `return()` is appropriate in multi-step helpers and callbacks, as in the examples, but is not mandatory everywhere.
- Keep R scripts quiet by default. Remove nonessential console messages and progress reporting, including `message()`, status-only `cat()` or `print()` calls, progress bars, and timers. Allow at most one or two essential messages across a full script run, not per loop iteration or function call. Preserve meaningful warnings, errors, required result output, and calls with computational side effects. When disabling progress reporting, preserve the underlying computation. Retain useful memory cleanup for large datasets; do not add `pbapply`, timers, `rm()`, or `gc()` as decoration.
- Always write R code for graphs. Preserve plotted data, transformations, scales, and statistical summaries unless visual or analytical changes are requested.

## Small style illustration

This illustrates layout and comment tone for an existing R workflow; it is not a template to impose on every script or a reason to introduce new packages.

```r
# Combine state admission files

library(data.table)
library(fst)

INPUT_DIR <- "processed/admissions-by-state"
OUTPUT_FILE <- "processed/admissions.fst"

# Read admissions ---------------------------------------------------------

paths <- Sys.glob(file.path(INPUT_DIR, "*.fst"))

dt <- lapply(paths, function(path) {
  read.fst(path, as.data.table = TRUE)
}) |>
  rbindlist(use.names = TRUE, fill = TRUE)

# Write out ---------------------------------------------------------------

write.fst(dt, OUTPUT_FILE, compress = 90)
```

## Apply and check

Read the supplied code and relevant local instructions before editing. Use the most recent user-provided style examples if they refine these preferences. Rename internal objects only when all references are understood; preserve names consumed by formulas, string-based lookups, saved objects, or downstream scripts.

For file edits, review the diff and parse the rewritten R code without executing it. When changes affect executable expressions, use existing checks or a small representative comparison of original and rewritten behavior if feasible. Do not run a full production analysis merely to check formatting. State whether the code was parsed, executed, or compared; do not imply numerical equivalence was tested when it was only reviewed.

For pasted code, return the complete rewritten code unless the user requests a patch or excerpt. For workspace edits, edit the requested files and give a short summary with file links and verification. Keep explanation brief; follow a request for code only. Describe the result as a style rewrite, without claiming human authorship or guaranteed AI-detector outcomes.
