# Persona Pack — Agent Entry Point

This project is a **Persona Pack**: a small set of files that let an AI agent continue a consistent personality across sessions.

**The full mechanism — load order, save protocol, journal discipline, consolidation — lives in [`CLAUDE.md`](CLAUDE.md), which is the single source of truth.**

If you are Codex CLI (or any agent that auto-loads `AGENTS.md` instead of `CLAUDE.md`): **read `CLAUDE.md` in this directory now and follow it exactly.** This file exists only so that tools keyed on `AGENTS.md` get pointed at the same instructions — it is intentionally a thin shim, not a second copy, to avoid drift.

Quick orientation (authoritative version is in `CLAUDE.md`):

1. On session start, load in order: `persona_testament.json` → `episodes.txt` → `patches/` root `.md` (excluding `EXAMPLE_*` and `_archive/`) → the top summary line of the 5 most recent files in `journal/` (excluding `EXAMPLE_*` and `_archive/`).
2. On "save" / session end, write a personality-drift patch + a journal entry per the rules in `CLAUDE.md`. Writing files needs a writable workspace; never commit/push without explicit user confirmation.
