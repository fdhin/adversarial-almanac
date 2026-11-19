---
name: "shelling"
categories:
  - blackarch-misc
version: "227.0a6c135"
homepage: "https://github.com/ewilded/shelling"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-23"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ewilded/shelling"
github-stars: 441
github-pushed: "2020-03-16"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shelling

> Burp Suite extension that generates OS command-injection payload sets which get past improperly written sanitizers.

## What it's for

SHELLING (by ewilded) is a Burp Suite extension for detecting OS command and argument injection that fixed payload lists miss. Rather than a static wordlist, it composes payload sets from the anatomy of the flaw: the target's syntax context (unquoted, single-quoted or double-quoted expressions), command separators per platform (`;`, `&`, `|`, `%0a`, the `.bat`-only `%1a`), argument separators that survive space filtering (`$IFS$9`, tabs, brace expressions, `%ProgramFiles:~10,1%`), string terminators and encodings — a few hundred payloads by default, thousands with every option enabled. It integrates with Burp in three ways: as an Intruder payload generator (payload type "Extension generated" → Command injection), as an active-Scanner extension that files issues like native scanner findings, and as an exporter that writes the generated payload list to clipboard or file for tools outside Burp. Its auto mode wires the feedback channels for you — DNS callbacks through Burp Collaborator, with every payload marked into its own subdomain so any interaction maps back to the exact payload that triggered it, and time-based delays (`sleep`/`ping -n`) for blind injection; manual mode lets you specify arbitrary command/argument pairs (for example `touch /tmp/owned.PAYLOAD_MARK`) when the feedback channel is the filesystem or direct output. It also includes a single-byte generator for fuzzing separators and terminators, plus experimental argument-injection and terminal escape-sequence injection modes. It is Burp-only — no standalone command-line interface (the packaged `shelling` wrapper just launches the extension jar, which needs Burp to host it) — and full scanner integration requires Burp Pro, though Intruder and export work in Burp Community. An older build circulates in the BApp Store as "Command Injection Attacker".

*No corpus evidence — derived from upstream documentation.*
