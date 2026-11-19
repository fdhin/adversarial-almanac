---
name: "aurebeshjs"
categories:
  - blackarch-misc
version: "77.75a8fc6"
homepage: "https://github.com/aemkei/aurebesh.js"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-24"
build-system: scripts
license: custom:WTFPL
meta-checked: "2026-11-09"
github-repo: "aemkei/aurebesh.js"
github-stars: 55
github-pushed: "2022-12-02"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aurebeshjs

> Translate JavaScript source into lookalike scripts written in other writing systems.

*No corpus evidence — derived from upstream documentation.*

## What it's for

aurebesh.js (by aemkei/Martin Kleppe) is an obfuscation toy: it rewrites JavaScript so the code still runs unchanged but its identifiers, operators and literals are rendered with Unicode characters from other scripts — Japanese katakana and similar "other alphabets". The output is functionally equivalent JavaScript that is nearly unreadable to humans and to naive pattern-matching, which is the whole point: demonstrating that source-level filtering cannot rely on what script the characters are in. It is a browser-based converter (a single HTML page plus the translator script), not a general-purpose obfuscator — no minification, control-flow transforms, or string encryption.

## Typical use cases

### Serve the translator web UI locally

Run the packaged web app on a local port and open it in a browser.

```
aurebeshjs 8080
```

Outcome: serves the converter page at `http://127.0.0.1:8080`; paste JavaScript into the input and the page returns the equivalent script rewritten in the target writing system, ready to copy. The output still executes as JavaScript — verify by running it under `node` unchanged.
