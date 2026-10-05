---
name: brain-update
description: Summarise the current chat, cowork or code session and update the project's CLAUDE.md file(s) - add new durable knowledge, revise changed facts, remove superseded ones. Use when the user says "brain update", "update CLAUDE.md", "save this session to memory", "wrap up the session", or asks to sync project memory. Handles folders with multiple CLAUDE.md files.
---

# brain-update

Keeps a project's CLAUDE.md files (the project's "brain") accurate after each session. Plain-English purpose: read what happened, compare it with what the brain already says, then add what is new, fix what changed, delete what is no longer true.

Work autonomously. Only stop to ask when a step below says so.

## Step 1 - Find the brain files

Run from the working folder:

```
find . \( -iname "CLAUDE.md" -o -iname "CLAUDE.local.md" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/.brain-backups/*"
```

- Ignore `~/.claude/CLAUDE.md` (global) unless the user explicitly asks to update it.
- Always re-read each candidate from disk. The copy loaded into the session may be stale.
- No file access (plain chat, no folder)? Skip to Step 7 (paste-ready output).
- Zero files found? Offer to create `CLAUDE.md` in the working folder using the template in Step 6, then continue.

## Step 2 - Choose target file(s)

- **One file**: use it.
- **Multiple files**: ask once with AskUserQuestion, options:
  1. **Update one only** - then ask which (list paths, mark the best match by session content as recommended)
  2. **Update all** - route each fact to the most relevant file (Step 4)
  3. **Interview** - ask short questions (max 4 per round) about where each uncertain fact belongs
- Unattended or no way to ask: default to "Update all" with routing, and state that choice at the top of the report.

## Step 3 - Extract session knowledge

Scan the whole session. Keep only **durable** items:

- Decisions and the reason behind them
- Architecture, structure, file locations, naming conventions
- Commands, tools, versions, configuration that work
- Verified failures / dead ends (what was tried, why it failed) so they are never repeated
- Current status and open items / next steps
- User preferences about how to work on this project

Discard: chit-chat, one-off debugging noise, unverified guesses, anything already obvious from the code.

**Never write secrets** (API keys, passwords, tokens, private keys). Record only where they live (e.g. "key stored in .env as SUPABASE_KEY"). Flag personal or commercially sensitive details for the user instead of writing them.

## Step 4 - Reconcile and route

For each extracted item compare against the target file(s) and label it:

| Label | Action |
|---|---|
| NEW | Add under the best-fitting heading |
| CHANGED | Rewrite the existing line in place (do not keep both versions) |
| SUPERSEDED | Delete the old line |
| DUPLICATE | Skip |
| UNSURE | Keep the existing line, add to the report as a flag |

Removal rules:
- Delete only when the session clearly shows the old statement is no longer true.
- Do not delete user-written rules or instructions unless the user contradicted them in this session.
- Completed items under "Next steps" are removed or moved to a one-line status note.

Routing across multiple files: put each fact in the file whose folder it concerns. Shared, project-wide facts go in the root file. Child files hold only what is specific to their folder. Never duplicate a fact across files; link by path instead.

## Step 5 - Write safely

1. If the folder is not a git repo, copy each target to `.brain-backups/<name>-<YYYYMMDD-HHMM>.md` first. In a git repo, skip the backup (git is the undo).
2. Edit in place with the Edit tool. Preserve existing headings, order and tone; do not reformat untouched sections.
3. Use absolute dates (e.g. 2026-10-05), never "today" or "last week".
4. Keep each file lean. If one exceeds about 200 lines, say so in the report and suggest splitting by folder rather than trimming facts silently.
5. Update or add a single line near the top: `Last brain-update: YYYY-MM-DD`.

## Step 6 - Template for a new CLAUDE.md

```
# <Project name>
Last brain-update: <date>

## Purpose
## Structure and key files
## Conventions and decisions
## Commands and setup
## Known dead ends (do not retry)
## Current status
## Next steps
```

## Step 7 - Report (and chat-only fallback)

Report briefly, per file: Added / Changed / Removed counts with one-line items, then any UNSURE flags and any sensitive items left out. No recap of the whole session.

In plain chat with no file access, output the proposed additions, changes and deletions as a single paste-ready block per intended file (headed with the suggested path), and offer to apply it once a folder is connected.
