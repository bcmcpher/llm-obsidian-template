---
name: karpathy-wiki-ingest
description: Ingest a new source into the personal Karpathy-style Obsidian wiki vault. Use this skill whenever the user wants to save a URL, bookmark an article, video, paper, or docs page, record meeting notes, or promote saved content into concept atoms or glossary terms. Triggers on: "save this", "add to wiki", "ingest", "bookmark this", "I want to remember this article/video/paper", "add this to my notes", "capture this link", "take notes on this meeting", or any time the user shares a URL alongside research intent. Also triggers when the user says "create an atom for X", "add a glossary term for Y", or "update my wiki with this".
---

# Karpathy Wiki Ingest

**Vault root:** `/home/bcmcpher/Projects/claude/karpathy-wiki`

This vault uses a layered structure: raw sources feed concept atoms, which feed topic maps. Your job here is to create a well-formed source note, connect it to existing atoms, and optionally seed new atoms — keeping the graph growing without duplicating work.

For the relationship taxonomy and full field definitions, read: `references/vault-schema.md`

---

## Quick Reference: Source Types

| Medium | Folder | Clues |
|--------|--------|-------|
| Web article / blog | `sources/web/` | General URLs, blog posts, newsletters |
| YouTube / video | `sources/video/` | `youtube.com`, `youtu.be`, Vimeo, conference talks |
| Academic paper | `sources/paper/` | `arxiv.org`, `doi.org`, journal sites |
| Docs / API reference | `sources/docs/` | `docs.*`, `*.readthedocs.io`, official library sites |
| Meeting / discussion | `sources/meeting/` | No URL; in-person or virtual conversation |

---

## Workflow

### 1. Classify the source
Infer the medium from the URL or context. If genuinely ambiguous, ask.

### 2. Determine filename
Pattern: `YYYY-MM-DD-kebab-title.md`  
**Use today's date (the date you are saving it) — never the publication date.** A 2020 paper saved today gets `2026-04-27-paper-title.md`, not `2020-01-01-paper-title.md`.  
Derive the kebab slug from the title (drop articles, punctuate with hyphens, max ~6 words).

- `2026-04-27-attention-is-all-you-need.md`
- `2026-04-27-karpathy-makemore-series.md`
- `2026-04-27-pytorch-dataloader-docs.md`
- `2026-04-27-team-rag-architecture-sync.md` ← meetings use context not title

Confirm the filename with the user before writing if there's any ambiguity.

### 3. Fill the template
Write the file to the correct subfolder. Required fields per medium:

**All sources:** `title`, `medium`, `saved` (today), `tags`, `status: unread`  
**Web/video/paper/docs:** also `url`  
**Video:** also `channel`  
**Paper:** also `authors[]`, `year`  
**Docs:** also `tool`, optionally `version` and `section`  
**Meeting:** `date` instead of `saved`, `attendees[]`, `context`, `status: unprocessed`

Ask the user for:
- **Why Saved** — what specific question or project prompted this? (1–3 sentences, their own words)
- **Summary** — 2–3 sentence digest of what the source says. If you fetched the URL, offer a draft for them to edit.
- **Key Points** — bullet list of takeaways. Offer to draft from fetched content.

Never reproduce full article text. Summaries only.

### 4. Wire up connections
Ask which existing atoms or concepts this source relates to. Check `atoms/` for likely candidates (grep titles or tags). Then write the Dataview inline fields under `## Connections`:

```
supports:: [[Relevant Atom]]
introduces:: [[New Concept]]
related:: [[Adjacent Concept]]
```

For papers, also check if it builds on known work: `cites:: [[Prior Atom]]`

If unsure, use `related::` as a holding pattern — it can be refined during the monthly LLM-assisted review.

### 5. Promote to atoms (optional but encouraged)
If the source introduces a concept not yet in `atoms/`, offer to create a stub. A good atom candidate is any concept that:
- Appears in the source's title or abstract
- Would be referenced from multiple future sources
- Isn't already covered by an existing atom

Stub atom format (save to `atoms/kebab-concept.md`):
```markdown
---
title: Concept Name
aliases: []
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
confidence: low
---

## Summary
<!-- One-sentence summary from this source -->

## Detail
<!-- Expand later -->

## Sources
cites:: [[source-filename]]

## Connections
part-of:: 
related:: 
```

Set `confidence: low` for single-source atoms. Upgrade to `medium` when two or more independent sources support it.

### 6. Add glossary terms (optional)
If the source defines a specific technical term clearly, offer a glossary stub:
```
glossary/term-name.md
```
Glossary entries are lighter than atoms — just definition + usage notes.

### 7. Update the ingest log
Append to `_meta/log.md`:
```markdown
## [YYYY-MM-DD] <medium> | <Title>
url:: <url or n/a>
atoms:: [[Atom A]], [[Atom B]]
```

---

## Archive (optional)
If the user wants to preserve the full source text locally (for offline access or link-rot protection), save it to:
```
.archive/YYYY-MM-DD-slug.md
```
Then add to the source note: `raw:: .archive/YYYY-MM-DD-slug.md`

The `.archive/` folder is gitignored and excluded from Obsidian's indexer — it won't appear in the graph.

---

## Common Mistakes to Avoid
- Don't ingest a source that's already in `sources/` under a different filename — check before creating
- Don't create an atom for a term that already exists in `glossary/` or vice versa
- Don't leave `related::` as the only connection on every note — push for `supports::` or `introduces::` when the relationship is clear
- Meetings don't have `url` fields; don't add one
