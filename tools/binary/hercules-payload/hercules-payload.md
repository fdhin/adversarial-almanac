---
name: "hercules-payload"
categories:
  - blackarch-binary
version: "222.2607a3a"
homepage: "https://github.com/EgeBalci/HERCULES"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "EgeBalci/HERCULES"
github-stars: 601
github-pushed: "2021-07-18"
github-archived: true
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hercules-payload

> Interactive generator for obfuscated Windows payloads with AV-evasion options.

## What it's for

HERCULES (packaged as `hercules-payload`) is a 2016-era Go tool that builds
obfuscated Windows payload executables through an interactive wizard. After
you answer prompts for the payload type and connection parameters it applies
the evasion features you selected: UPX packing of the result, a persistence
function that registers the payload in the Windows startup registry key
`CurrentVersion/Run`, and a migration function that repeatedly attempts to
inject into a remote process until it succeeds. Its "AV evasion score"
heuristic (1–10) estimates how each option trades stealth against reliability
— packing and the special functions lower the score. The upstream repository
is archived and its techniques have been known to endpoint vendors for years:
treat it as a historical artifact and a teaching example of payload-generation
design, and evaluate it only against your own lab detection stack.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a payload through the wizard

```
hercules-payload
```

Outcome: starts the interactive generator; after you answer the prompts
(payload type, ATTACKER_IP and port for call-backs, UPX packing, persistence
and migration toggles) it produces the payload executable, ready to run
against a lab target and connect back to your listener.

The upstream tool is driven entirely by this single interactive prompt — there
are no documented command-line flags, so batch use requires feeding stdin.
