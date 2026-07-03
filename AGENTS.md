# Agent Instructions — VramJanitor

VramJanitor is a small local-first CLI for local LLM users who need to reclaim desktop VRAM before inference.

## Rules

- Keep the MVP local-only. Do not add telemetry, hosted dashboards, or account auth.
- Do not implement automatic process killing until a later explicit design review.
- Treat command output as potentially sensitive. Redact full command lines, usernames, hostnames, and paths in exported reports.
- Prefer fixture-based tests so CI does not require an NVIDIA GPU.
- Use Beads (`bd`) for all task tracking. Do not use markdown TODOs or other trackers.
- Use conventional commits.

## Verification

Before claiming work is done, run the relevant tests plus:

```bash
git status --short
bd list --json >/tmp/vram-janitor-beads.json
```
