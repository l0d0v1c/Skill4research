# Research CLAUDE.md

A `CLAUDE.md` skill for [Claude Code](https://claude.ai/code) by **Luc Emile Brunet** ([l0d0v1c](https://github.com/l0d0v1c)) and **pseudoLuc**. It extends Claude Code's native coding workflow to diverse scientific research domains that do not necessarily involve coding — literature review, experimental design, data analysis, scientific writing, and more.

Coding is a special case of structured language production. This skill treats it as such.

## What it does

Drop `CLAUDE.md` at the root of any project. Claude Code reads it automatically and shifts from code-first assistant to research partner:

- **Asks before acting** — clarification questions one at a time, never in batches
- **Proposes alternatives** — 2 to 4 options per non-trivial task, each scored by effort (1-5)
- **Maintains canonical files** — a structured set of markdown files that accumulate knowledge across sessions
- **Verifies references** — DOIs checked via Crossref, ISBNs via WorldCat, no fabricated citations
- **Triggers status reviews** — structured brainstorming when progress stalls
- **Enforces reproducibility** — fixed seeds, exact versions, explicit install commands

## Canonical files

The skill organizes project knowledge into seven files at the root, each with a single purpose:

| File | Purpose |
|---|---|
| `spec.md` | Project specification — the source of truth |
| `session_log.md` | Session journal: date, work done, current state |
| `findings.md` | Structural observations that feed back into the spec |
| `surprises.md` | Unexpected discoveries and anomalies |
| `rex.md` | Lessons learned: failures and how they were resolved |
| `biblio.md` | Verified bibliographic references in BibTeX format |
| `requirements.md` | Dependencies with exact versions and install commands |

All entries are cumulative and dated. Nothing is overwritten.

## Setup

```bash
cp CLAUDE.md /path/to/your/project/
```

Start a Claude Code session in that project. The directives apply immediately.

## License

MIT


# Citation
[![DOI](https://zenodo.org/badge/1214280117.svg)](https://doi.org/10.5281/zenodo.19641745)
