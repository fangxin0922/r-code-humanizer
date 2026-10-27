# R Code Humanizer

A Codex skill for rewriting R code in a practical research-script style while preserving analytical behavior.

It improves organization, naming, and comments using principles drawn from research data-processing scripts. Scientific definitions stay visible, functions represent meaningful tasks, and scripts run quietly.

## What it does

- Organizes code around inputs, definitions, transformations, and outputs.
- Uses readable names, concise comments, and simple section headings.
- Chooses loops, pipes, and vectorized operations according to clarity.
- Keeps useful functions without introducing unnecessary frameworks.
- Removes routine console messages, progress bars, and timers. At most one or two essential status messages may remain across a full run.
- Preserves meaningful warnings, errors, and required analytical output.

Style edits should preserve data handling, model specifications, seeds, input and output paths, and numerical results. The skill keeps existing package choices when changing them could affect behavior, and uses R for graphs.

## Install in Codex

With Git installed, run the following in a macOS or Linux terminal:

```sh
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/fangxin0922/r-code-humanizer.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/r-code-humanizer"
```

This requires the destination folder not to exist. If you already have a manually installed copy, keep a backup or compare it with this repository before replacing it.

Alternatively, download the repository ZIP, extract it, and place its contents in a folder named `r-code-humanizer` under your Codex skills directory. `SKILL.md` should sit directly inside that folder, with `agents/` and `references/` alongside it.

Start a new Codex task after installation. If the skill is not available yet, restart Codex.

## Use

Invoke the skill and provide your R code or a script path:

```text
Use $r-code-humanizer to rewrite analysis.R in a practical research style.
Preserve the analysis behavior and keep console messages minimal.
```

You can also paste code after invoking `$r-code-humanizer` or ask it to humanize a specific R file. Review the changes and the reported checks before running the revised analysis.

## Style principles

The style favors code that lets a reader find important research decisions and follow the data workflow. Comments explain domain meaning, exceptions, and intent. Naming detail grows with scope. Abstraction is useful when it represents a coherent task; separate blocks can remain when their scientific decisions differ.

These are coding preferences derived from two reference scripts, not a test of human authorship. The skill does not introduce deliberate imperfections or promise AI-detector outcomes. Its quiet-output rule takes priority over messages and timers in the original examples.

Read the [full skill instructions](SKILL.md) and [reasoning behind the style rules](references/style-reasoning.md).

## Files

```text
SKILL.md                       Skill instructions
agents/openai.yaml             Codex display metadata and invocation prompt
references/style-reasoning.md  Evidence, decision rules, and examples
```

This repository contains instructions and documentation, not an R package or an automatic code formatter. Any R packages used by your own analysis remain that analysis's dependencies.

## Updating a Git-based installation

```sh
git -C "${CODEX_HOME:-$HOME/.codex}/skills/r-code-humanizer" pull --ff-only
```

Keep any local customizations committed or backed up before updating.
