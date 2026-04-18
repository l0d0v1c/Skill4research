# CLAUDE.md skill user guide

**Author:** Luc Emile Brunet (l0d0v1c) and pseudoLuc
**Version:** 1.1
**License:** MIT

---

## 1. What is this skill?

`CLAUDE.md` is a directives file that transforms Claude Code from a coding assistant into a **structured research partner**. It applies the principles of Luc Emile Brunet's RDI (Research Development Innovation) methodology, including:

- **Producing alternatives** with effort estimation before any action.
- **Cumulative traceability** of observations, anomalies, and lessons learned.
- **Systematic verification** of bibliographic references.
- **Status reviews** triggered by stalling signals.

These principles are detailed in:

> BRUNET, L. E. *Créativité humaine et augmentée au service de l'innovation.* vol. AG5210V2, Editions Techniques de l'ingénieur, 2021.
>
> BRUNET, L. E. *Recherche développement innovation (RDI) en entreprise.* Techniques de l'ingénieur, base documentaire TIB564DUO, 2016.

Coding is a special case of structured language production. This skill treats any investigation — literature review, experimental design, data analysis, scientific writing — with the same rigor.

---

## 2. Installation

### In Claude Code (CLI, desktop, web, IDE)

Copy the `CLAUDE.md` file to the root of your project:

```bash
cp CLAUDE.md /path/to/your/project/
```

Claude Code automatically detects the file and applies the directives from the start of the session.

### As a prompt (without Claude Code)

If you use Claude via the API or conversational interface without Claude Code, paste the full content of `CLAUDE.md` into the **system prompt** or at the beginning of your conversation. The effect is the same: Claude adopts the described behavior.

---

## 3. Methodology

### 3.1. Session startup

At the beginning of each new session, Claude follows a strict protocol:

1. Read `spec.md` — the project's source of truth.
2. Read `session_log.md` — to recover context from the previous session.
3. Read `findings.md`, `surprises.md`, `rex.md` if relevant.
4. Read `requirements.md` before any installation.
5. State in two lines the understood project state before acting.

This protocol guarantees **continuity between sessions**: nothing is lost, nothing is guessed.

### 3.2. Clarifications and alternatives

Before producing code or text, Claude:

- Asks **clarification questions one at a time** (never in batches).
- Proposes **2 to 4 alternatives** for any non-trivial task.

Each alternative is annotated with an **effort score**:

| Score | Meaning |
|---|---|
| **1** | A few minutes, local modification |
| **2** | One to two hours, simple implementation |
| **3** | One day, integration of several components |
| **4** | Several days, design or partial refactoring |
| **5** | A week or more, structural work |

Expected format:

```markdown
**Option 1 — Short name**
Description in two to four lines. What is done, what is obtained.
Trade-off: what is gained, what is lost.
Effort: **2** (a few hours)
```

### 3.3. Status review

Claude triggers a **status review** when:

- Three iterations without measurable progress.
- The same error recurring twice without resolution.
- Divergence between the project state and `spec.md`.
- A sense that the initial framing no longer holds.

The review follows a precise format: problem in one sentence, active hypotheses, constraints, 2 to 3 recovery paths.

### 3.4. Reproducibility

- Fixed seeds in any stochastic code.
- Exact versions in `requirements.md` (`==1.2.3`, never `>=1.2`).
- Explicit install commands, not just package names.

---

## 4. Canonical files

The skill organizes project knowledge into seven files at the root. Each file has a single purpose, no overlap. Additions are **cumulative and dated** — never overwritten, always extended.

### 4.1. `spec.md` — Project specification

The source of truth. Everything else refers to this file. Claude reads it first at every session.

### 4.2. `session_log.md` — Session journal

A lightweight entry at each session end: date, work done, current state, pending tasks, next steps.

```markdown
## 2026-04-18 — Session 7

**State at session start:** training pipeline functional,
corpus cleaned, first baseline in place (accuracy 0.71).

**Done:**
- Added evaluation module on test set.
- Comparative benchmark against HuggingFace baseline.

**Pending:**
- Check if the class bias observed holds on a second corpus.

**Next session:** resume by analyzing the class bias.
```

### 4.3. `findings.md` — Structural observations

Observations that feed back into the specification. Not one-off anomalies.

```markdown
## 2026-04-18 — Embeddings stabilize after 3 epochs
Across 5 test runs (varied seeds), validation loss plateaus from
epoch 3 onwards. Epochs 4-10 bring no measurable improvement.
**Implication:** training budget can be divided by 3.
```

### 4.4. `surprises.md` — Unexpected discoveries

Anomalies, unforeseen behaviors. Not necessarily structural.

```markdown
## 2026-04-18 — BPE tokenizer splits URLs at "https"
The default tokenizer treats `https://` as a single token followed
by a fragment. Input URLs lose their structure.
Non-blocking for now.
```

### 4.5. `rex.md` — Lessons learned

Failures encountered and how they were resolved.

```markdown
## 2026-04-18 — Memory crash on batch > 32
**Problem:** OOM on 24 GB GPU with batch_size=64.
**Cause:** misconfigured gradient accumulation, unreleased buffer.
**Resolution:** `torch.cuda.empty_cache()` after each accumulation
+ effective batch_size=32 with accumulation=2.
**Resolution time:** 3h.
```

### 4.6. `biblio.md` — Bibliographic references

In BibTeX format. Each reference is verified (see section 5).

### 4.7. `requirements.md` — Dependencies

Libraries, software, models, and tools needed with exact versions and install commands.

---

## 5. Bibliography management

### 5.1. Reference verification

The skill enforces systematic verification for each reference:

1. **DOI**: verified via the Crossref API (`https://api.crossref.org/works/{doi}`).
2. **ISBN**: verified via WorldCat or equivalent.
3. **URL**: page fetched to confirm existence.

If verification fails, the reference is marked `% UNVERIFIED` and flagged to the user. **No reference is ever fabricated.**

### 5.2. BibTeX format in `biblio.md`

```bibtex
@article{vaswani2017attention,
  author  = {Vaswani, Ashish and Shazeer, Noam and Parmar, Niki and others},
  title   = {Attention is all you need},
  journal = {Advances in Neural Information Processing Systems},
  year    = {2017},
  volume  = {30},
  doi     = {10.48550/arXiv.1706.03762},
  url     = {https://arxiv.org/abs/1706.03762}
}
% Verified: Crossref OK (2026-04-18), URL fetched OK.
```

### 5.3. Importing `biblio.md` into Zotero

The `biblio.md` file contains valid BibTeX. To import it into Zotero:

**Method 1 — Direct import**

1. Extract BibTeX entries from `biblio.md` into a `.bib` file:
   ```bash
   grep -A 20 '^@' biblio.md | grep -v '^%' > biblio.bib
   ```
   Or simply copy the `@article{...}`, `@book{...}`, etc. blocks into a `biblio.bib` file.

2. In Zotero: **File > Import...** > select `biblio.bib` > choose target collection > **Next**.

3. Zotero creates an entry for each reference with all fields populated (authors, title, DOI, ISBN, URL).

**Method 2 — Better BibTeX (recommended)**

1. Install the [Better BibTeX for Zotero](https://retorque.re/zotero-better-bibtex/) plugin.
2. **File > Import...** > select `biblio.bib`.
3. Better BibTeX handles citation keys, special characters, and non-standard fields more robustly.
4. Advanced: configure **automatic sync** so Zotero watches the `.bib` file and imports new entries as they appear.

**Method 3 — Via DOI in Zotero**

Since the skill verifies DOIs, you can also:

1. In Zotero: click the **Add by Identifier** icon (magic wand) in the toolbar.
2. Paste the DOI (e.g., `10.48550/arXiv.1706.03762`).
3. Zotero automatically retrieves complete metadata from Crossref.

This method is the most reliable as Zotero uses publisher metadata directly.

**Tip: extraction script**

To automate DOI extraction from `biblio.md`:

```bash
grep -oP 'doi\s*=\s*\{([^}]+)\}' biblio.md | sed 's/doi\s*=\s*{\(.*\)}/\1/'
```

---

## 6. Typical investigation workflow

Here is a typical workflow for using the skill in a scientific investigation:

### Step 1 — Project initialization

```
mkdir my-investigation
cp CLAUDE.md my-investigation/
cd my-investigation
claude   # or open Claude Code in this directory
```

Claude asks you to create `spec.md` — the starting point of any investigation.

### Step 2 — Writing the specification

Describe your research question, scope, and constraints. Claude helps structure it through clarification questions asked one at a time.

### Step 3 — Exploration and alternatives

For each task (literature review, method selection, analysis), Claude proposes 2 to 4 alternatives with effort scores. You choose, it executes.

### Step 4 — Knowledge accumulation

Across sessions:
- Structural observations go into `findings.md`.
- Anomalies into `surprises.md`.
- Failures and resolutions into `rex.md`.
- Verified references into `biblio.md`.
- Session journal into `session_log.md`.

### Step 5 — Status reviews

If work stalls, Claude triggers a structured review: problem, hypotheses, constraints, recovery paths.

### Step 6 — Synthesis

At project end, the canonical files constitute a **complete and traceable knowledge base** of the investigation.

---

## 7. Usage without Claude Code (as a prompt)

If you don't have access to Claude Code, the content of `CLAUDE.md` works as a **system prompt**. Paste it at the beginning of your conversation with Claude (API, web interface, or any other client).

Limitations compared to Claude Code:

| Feature | Claude Code | Prompt only |
|---|---|---|
| Automatic reading of canonical files | Yes | No — you must paste the content |
| File creation and updates | Yes | No — you must copy outputs manually |
| Subagent delegation | Yes | No |
| DOI/ISBN verification via web | Yes | Depends on the client |
| Cross-session continuity | Automatic via `session_log.md` | Manual — paste the log at session start |

In prompt mode, the skill remains valuable for **methodological discipline**: alternatives with effort, clarifications before action, observation formats.

---

## 8. Best practices

1. **Always start with `spec.md`.** Without a specification, work drifts.
2. **Don't skip clarification questions.** They prevent unnecessary iterations.
3. **Trust the effort score.** An effort 4 underestimated as effort 2 produces technical debt.
4. **Feed `surprises.md` even for small anomalies.** What seems trivial today may be structural tomorrow.
5. **Export `biblio.md` to Zotero regularly.** BibTeX is an exchange format, not long-term storage.
6. **Re-read `session_log.md` at session start.** Claude does it automatically; you should too.

---

## 9. References

```bibtex
@article{brunet2021creativite,
  author    = {Brunet, Luc Emile},
  title     = {Créativité humaine et augmentée au service de l'innovation},
  journal   = {Techniques de l'ingénieur},
  year      = {2021},
  volume    = {AG5210V2},
  publisher = {Editions Techniques de l'ingénieur}
}

@article{brunet2016rdi,
  author    = {Brunet, Luc Emile},
  title     = {Recherche développement innovation (RDI) en entreprise},
  journal   = {Techniques de l'ingénieur},
  year      = {2016},
  note      = {Base documentaire TIB564DUO, Management et ingénierie
               de l'innovation}
}
```
