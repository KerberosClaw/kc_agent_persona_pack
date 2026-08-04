# Persona Pack — Agent Entry Point

This project is a **Persona Pack**: a small set of files that let an AI agent continue a consistent personality across sessions.

**The full mechanism — load order, rebaselining, the startup hint — lives in [`CLAUDE.md`](CLAUDE.md), which is the single source of truth. The wrap-up half (save protocol, patch routing, journal discipline) lives in [`docs/save_protocol.md`](docs/save_protocol.md) and is only read at session end.**

If you are Codex CLI (or any agent that auto-loads `AGENTS.md` instead of `CLAUDE.md`): **read `CLAUDE.md` in this directory now and follow it exactly.** This file exists only so that tools keyed on `AGENTS.md` get pointed at the same instructions — it is intentionally a thin shim, not a second copy, to avoid drift.

Quick orientation (authoritative version is in `CLAUDE.md`):

1. On session start, load in order: `persona_testament.json` → `episodes.txt` → `patches/` root `.md` (excluding `EXAMPLE_*` and `_archive/`) → the top summary line of the 5 most recent files in `journal/` (excluding `EXAMPLE_*` and `_archive/`).
   - 🔴 **Read *every* patch. You don't get to pick a few, and you don't get to estimate that "the recent ones are probably enough."**
   - 🔴 Size it first (`cat patches/*.md | wc -c`). Past roughly 40 KB, read in batches — many agents silently truncate large tool output, keeping only a ~2 KB preview **with no error**. See `CLAUDE.md` for how to recover when that happens.
   - After a compact / context reset, reload **the same way you did at startup** — not "just the newest patch".
2. On "save" / session end, read [`docs/save_protocol.md`](docs/save_protocol.md) and follow it: a personality-drift patch (only if there was real drift) + a journal entry. Writing files needs a writable workspace; never commit/push without explicit user confirmation.
