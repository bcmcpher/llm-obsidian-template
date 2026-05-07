---
name: memex-read
description: Mark a source as read and optionally capture first-read reactions. Use when the user has read a saved source but hasn't processed it into atoms yet — bridging the unread → read → processed lifecycle. Triggers on: "I've read this", "mark as read", "I skimmed this", "I read this source", "read but not processed", "I went through this article/paper/video", "finished reading [source]". Not for full processing — use memex-connect when ready to wire atoms and connections.
---

# Karpathy Wiki Read

**Vault root:** `/home/bcmcpher/Projects/claude/memex-vault`

This skill closes the gap between `status: unread` (saved but not yet opened) and the `memex-connect` workflow (full graph wiring). It records that the user has read a source and optionally captures immediate reactions — impressions that are easy to lose before a formal processing session.

The `read` status means: *I've consumed this; I know what it contains; I haven't integrated it into the graph yet.*

---

## Workflow

### 1. Find the source
Accept a file path, title fragment, or URL. If the user gives a title or URL, find the file:

```bash
VAULT=/home/bcmcpher/Projects/claude/memex-vault

# By URL
grep -rl "<url>" "$VAULT/sources/"

# By title fragment
grep -rl "keyword" "$VAULT/sources/" | head -20
```

If multiple matches, present the list and ask which one. If no match, suggest using `memex-capture` or `memex-ingest` first.

Read the source file. Confirm its current `status:` — if already `processed`, say so and stop (no action needed). If `status: read`, confirm with the user before overwriting reactions.

### 2. Capture first-read reactions (optional)
Ask: **"Any quick reactions, highlights, or follow-up questions?"**

This is optional and deliberately low-friction — one to three bullets is enough. Accept:
- A claim the source makes that surprised them
- A point of confusion or disagreement
- A question the source raises but doesn't answer
- A concept name they want to look up later

If the user says nothing or says "no", skip this step entirely. Do not prompt again.

### 3. Update the source file
Two changes only:

**a. Set `status: read` in frontmatter:**
```yaml
status: read
```

**b. If reactions were provided, append a `## First Read` section** after `## Why Saved` and before `## Summary`:
```markdown
## First Read
<YYYY-MM-DD>
- <reaction bullet one>
- <reaction bullet two>
```

Do not modify any other section. Do not fill `## Summary` or `## Key Points` — that is `memex-connect`'s job.

### 4. Log
Append to `_meta/log.md`:
```markdown
## [YYYY-MM-DD] read | <source title>
url:: <url or n/a>
atoms:: 
skill:: memex-read
notes: <"reactions captured" or "no reactions"> — run memex-connect to integrate
```

### 5. Confirm and close
Report: file updated, status set to `read`. One line: run `memex-connect` when ready to wire this into the graph.

---

## What This Skill Does NOT Do

- Does not fetch the URL or update metadata
- Does not create atoms, glossary stubs, or Dataview connections
- Does not fill `## Summary` or `## Key Points`
- Does not advance status past `read` — use `memex-connect` for full processing
- Does not handle meeting notes — meetings use their own status (`unprocessed → processed`)

---

## Common Mistakes to Avoid
- Don't ask for a summary or key concepts — those belong in the connect step
- Don't mark a source `processed` — that requires atom wiring via `memex-connect`
- Don't modify the `## Connections` section or any relation fields
- Don't run if the source is already `processed` — nothing to do
