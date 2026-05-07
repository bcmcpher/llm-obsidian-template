---
name: memex-stale
description: Surface temporal decay in the vault — sources that have sat unread too long, atoms that haven't been updated in over a year, topics where all atoms are still low-confidence, and sources marked read but never integrated. Use when the user wants to audit what's gone stale, catch neglected captures, or prioritize what to process next. Triggers on: "find stale notes", "what's been sitting unread", "stale vault audit", "decay check", "what have I neglected", "what's overdue for processing", "show me what's been ignored". Read-only — surfaces findings and suggests which skill to run; makes no vault changes.
---

# Karpathy Wiki Stale Audit

**Vault root:** `/home/bcmcpher/Projects/claude/memex-vault`

This skill is a read-only decay detector. It finds four categories of staleness and reports them as a prioritized list. It never modifies vault files — it tells you what to act on, and which skill to use.

Run it monthly, before a compose session, or whenever the vault feels like it has grown faster than it's been processed.

---

## Checks

### Check 1 — Long-unread sources (> 90 days)
Sources saved with `status: unread` where `saved:` is more than 90 days ago.

```bash
VAULT=/home/bcmcpher/Projects/claude/memex-vault

# Find all unread sources with their saved dates
grep -rl "status: unread" "$VAULT/sources/" | xargs grep -l "saved:" | while read f; do
  saved=$(grep "^saved:" "$f" | head -1 | awk '{print $2}')
  echo "$saved $f"
done | sort
```

Flag any source where the saved date is > 90 days before today. Report: filename, medium, title, saved date, days elapsed.

### Check 2 — Read but not integrated
Sources with `status: read` — consumed but never processed into atoms.

```bash
grep -rl "status: read" "$VAULT/sources/"
```

For each hit, read its title and saved date. These are the highest-value targets: the user already knows the content, they just need to wire it.

### Check 3 — Stale atoms (not updated in > 12 months)
Atoms where `updated:` is more than 12 months before today.

```bash
grep -rl "updated:" "$VAULT/atoms/" | xargs grep -l "confidence:" | while read f; do
  updated=$(grep "^updated:" "$f" | head -1 | awk '{print $2}')
  confidence=$(grep "^confidence:" "$f" | head -1 | awk '{print $2}')
  echo "$updated $confidence $f"
done | sort
```

Flag atoms where `updated:` is > 12 months old. Include their confidence level — a stale `confidence: high` atom is more concerning than a stale `confidence: low` stub.

**Do not flag:** atoms with no `updated:` field (they've never been revised — that's different from stale). Mention the count of un-timestamped atoms separately.

### Check 4 — Underconfident topics
Topics where every atom in `covers::` has `confidence: low`.

```bash
ls "$VAULT/topics/concepts/" "$VAULT/topics/research/"
```

For each topic, read its `covers::` list, then check the `confidence:` field in each linked atom. If all atoms are `confidence: low`, flag the topic as underconfident.

---

## Output Format

Present findings grouped by check, most actionable first:

```
## Stale Vault Audit — YYYY-MM-DD

### Read but not integrated (Check 2) — N sources
These are highest priority: you've already read them.
| Title | Saved | Days elapsed |
|-------|-------|-------------|
| ...   | ...   | ...         |
→ Run: memex-connect

### Long-unread sources (Check 1) — N sources
| Title | Medium | Saved | Days elapsed |
|-------|--------|-------|-------------|
| ...   | ...    | ...   | ...         |
→ Run: memex-read (if you've read it) or memex-connect (to process directly)

### Stale atoms (Check 3) — N atoms
| Atom | Confidence | Last updated | Days elapsed |
|------|------------|-------------|-------------|
| ...  | ...        | ...          | ...         |
→ Run: memex-trust-audit or memex-refactor (revise) as appropriate

### Underconfident topics (Check 4) — N topics
| Topic | Atom count | All confidence: low |
|-------|------------|---------------------|
| ...   | ...        | yes                 |
→ Run: memex-connect (add sources) or memex-trust-audit

---
Total: N findings across 4 checks.
```

If a check finds nothing, say so in one line and move on — don't omit the section.

---

## After the Report

Do not propose changes or apply fixes. End with:

> "No changes made — this is a read-only audit. Use the suggested skills above to act on any findings."

If the user asks to act on a specific finding during this session, describe what skill to invoke and what to say to trigger it — but do not run it inline. Keep this skill scoped to detection only.

---

## What This Skill Does NOT Do

- Does not modify any vault file
- Does not update `status:`, `updated:`, or `confidence:` fields
- Does not write a log entry (read-only)
- Does not infer whether stale content is *wrong* — only that it hasn't been touched
- Does not flag sources in `status: processed` regardless of age — processed is terminal

---

## Common Mistakes to Avoid
- Don't flag `confidence: low` atoms as stale just because of confidence — only flag on the `updated:` age threshold
- Don't include `.archive/` or `_exports/` in any scan — those folders are not vault nodes
- Don't surface Check 3 findings for atoms that have no `updated:` field at all — report that count separately as "never timestamped"
- Don't overwhelm with findings — if Check 1 returns > 20 sources, cap the table at 10 and note the total count
