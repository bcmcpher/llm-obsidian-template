# Vault Schema Reference

This is a condensed version of `_meta/schema.md` bundled for offline reference.

## Relationship Fields (Dataview Inline)

Syntax: `relation:: [[Target]]` or `relation:: [[A]], [[B]]` in the note body (not frontmatter).

### Source → Atom/Topic
| Field | Meaning |
|-------|---------|
| `supports::` | Source provides evidence for a claim in this atom |
| `introduces::` | Source is where this concept first appears in the vault |
| `demonstrates::` | Source shows a concrete example of a concept |
| `cites::` | Source builds on or explicitly references another known work |

### Atom → Atom
| Field | Meaning |
|-------|---------|
| `extends::` | Builds on / specializes another concept |
| `uses::` | Applies or depends on another concept |
| `contradicts::` | Conflicts with another concept or claim |
| `part-of::` | Component of a larger concept — drives concept map membership |
| `related::` | Loosely connected (fallback; refine monthly) |

### Any → Source
| Field | Meaning |
|-------|---------|
| `cites::` | This note references that source as evidence |

### Any → Glossary
| Field | Meaning |
|-------|---------|
| `defines::` | This note is the canonical definition for this term |

### Topic → Atoms
| Field | Meaning |
|-------|---------|
| `covers::` | This concept map covers these atoms |

## Status Values

**Sources:** `unread` → `read` → `processed`  
**Meetings:** `unprocessed` → `processed`  
**Projects:** `active` / `paused` / `complete` / `abandoned`  
**Atoms (confidence):** `low` / `medium` / `high`

## Naming Conventions

| Type | Pattern |
|------|---------|
| Sources (non-meeting) | `YYYY-MM-DD-kebab-title.md` |
| Meetings | `YYYY-MM-DD-kebab-context.md` |
| Atoms | `kebab-concept-name.md` |
| Glossary | `term.md` |
| Concept maps | `kebab-domain.md` |
| Projects | `proj-kebab-name.md` |
| Research | `rq-kebab-question.md` |
