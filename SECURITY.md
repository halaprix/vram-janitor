# Security Policy

## Supported versions

The project is pre-release. Security fixes target `main` until the first stable release.

## Reporting a vulnerability

Open a private security advisory on GitHub when available. If that is not available, open a minimal public issue that describes impact without exploit details.

## Security principles

- No telemetry by default.
- No automatic process killing in the initial MVP.
- Redact paths, usernames, hostnames, and full command lines from exported reports.
- Do not collect API keys, tokens, shell history, or model prompts.
