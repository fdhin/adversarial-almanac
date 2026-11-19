---
name: "microsploit"
categories:
  - blackarch-backdoor
version: "9.441e132"
homepage: "https://github.com/Screetsec/Microsploit"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-20"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Screetsec/Microsploit"
github-stars: 445
github-pushed: "2017-07-11"
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

# microsploit

> Menu-driven wrapper that pairs Metasploit payload generation with Office document templates to turn out weaponized Word/Office files quickly.

## What it's for

Generating malicious Office documents without hand-writing msfvenom commands. It is a bash menu (the `Microsploit` script) that drives Metasploit modules against bundled templates to produce backdoored documents: Microsoft Office on Windows/macOS, Open Office on Linux, macro-based attacks, and a Word buffer-overflow exploit. Aimed at fast social-engineering payload creation during authorized tests — it produces the booby-trapped file, but delivery (email, share, lure) and listener infrastructure are up to you. It is 2017-era, unmaintained, and requires a working Metasploit (`msfvenom`/`msfconsole`) on the box; expect its bundled exploit templates to be caught by modern Office macro blocking.

## Typical use cases

### Generate a weaponized Office document from the menu

```
./Microsploit
```

Outcome: opens a numbered interactive menu of document-exploitation modules (Office/Open Office macro attacks, Word buffer overflow). Select a number and answer the prompts (payload and listener details); the tool generates the booby-trapped Office file plus the matching Metasploit handler instructions. Every later step is the same menu-driven prompt flow — there are no documented command-line flags.

*No corpus evidence — derived from upstream documentation.*
