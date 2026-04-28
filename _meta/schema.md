# Schema

This file is the vault's constitution. Update it when adding or retiring relationship types, node types, status values, or naming conventions. All other notes defer to it.

---

## Node Types

| Type | Folder | Granularity | Required Fields |
|------|--------|-------------|-----------------|
| Source (web) | `sources/web/` | One file per URL | `title`, `url`, `medium`, `saved`, `status` |
| Source (video) | `sources/video/` | One file per URL | `title`, `url`, `medium`, `channel`, `saved`, `status` |
| Source (paper) | `sources/paper/` | One file per URL | `title`, `url`, `medium`, `authors`, `year`, `saved`, `status` |
| Source (docs) | `sources/docs/` | One file per URL | `title`, `url`, `medium`, `tool`, `saved`, `status` |
| Source (meeting) | `sources/meeting/` | One file per meeting | `title`, `medium`, `date`, `status` |
| Atom | `atoms/` | One concept per file | `title`, `created`, `confidence` |
| Glossary | `glossary/` | One term per file | `title`, `term`, `domain` |
| Concept map | `topics/concepts/` | One domain per file | `title`, `topic-type` |
| Project | `topics/projects/` | One project per file | `title`, `topic-type`, `status` |
| Research | `topics/research/` | One research question per file | `title`, `topic-type`, `question` |

---

## Relationship Types (Dataview Inline Fields)

Syntax: `relation:: [[Target Note]]` or `relation:: [[A]], [[B]]` in note body.

### Source → Atom/Topic
| Field | Meaning | Valid source → target |
|-------|---------|----------------------|
| `supports::` | Source provides evidence for a claim | source → atom, topic |
| `introduces::` | Source first introduces or defines a concept | source → atom, glossary |
| `demonstrates::` | Source shows a practical example | source → atom |

### Atom → Atom
| Field | Meaning | Notes |
|-------|---------|-------|
| `extends::` | Builds on / specializes another concept | Use when A is a subtype or elaboration of B |
| `uses::` | Applies or depends on another concept | Use when A requires B to function |
| `contradicts::` | Conflicts with another concept or claim | Use sparingly; document tension in body |
| `part-of::` | Component of a broader concept | Drives Concept map membership |
| `related::` | Loosely connected | Fallback only — review monthly for precision |

### Any Note → Source
| Field | Meaning |
|-------|---------|
| `cites::` | This note references that source as evidence |

### Any Note → Glossary
| Field | Meaning |
|-------|---------|
| `defines::` | This note elaborates or is the canonical definition for this term |

### Topic → Atoms
| Field | Meaning |
|-------|---------|
| `covers::` | This concept map / research note covers these atoms |

---

## Status Values

### Sources
| Value | Meaning |
|-------|---------|
| `unread` | Saved, not yet read |
| `read` | Read, not yet processed into atoms |
| `processed` | Connections and atoms created |

### Meeting
| Value | Meaning |
|-------|---------|
| `unprocessed` | Notes taken, follow-ups not yet acted on |
| `processed` | Action items done, follow-up sources captured |

### Atoms
| Value (confidence) | Meaning |
|--------------------|---------|
| `low` | Single source, speculative |
| `medium` | Multiple sources or well-established |
| `high` | Extensively sourced, cross-validated |

### Projects
| Value | Meaning |
|-------|---------|
| `active` | In progress |
| `paused` | On hold |
| `complete` | Finished |
| `abandoned` | Dropped |

---

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Sources (all except meeting) | `YYYY-MM-DD-kebab-title.md` | `2026-04-27-attention-is-all-you-need.md` |
| Meetings | `YYYY-MM-DD-kebab-context.md` | `2026-04-27-team-rag-sync.md` |
| Atoms | `kebab-concept-name.md` | `transformer-architecture.md` |
| Glossary | `term.md` (lowercase, hyphenated) | `self-attention.md` |
| Concept maps | `kebab-domain.md` | `deep-learning.md` |
| Projects | `proj-kebab-name.md` | `proj-rag-pipeline.md` |
| Research | `rq-kebab-question.md` | `rq-scaling-laws-llms.md` |

---

## Archive

Full source text may optionally be saved to `.archive/YYYY-MM-DD-slug.md`. This folder is gitignored and excluded from Obsidian's indexer. Reference it from a source note with:

```
raw:: .archive/2026-04-27-slug.md
```
