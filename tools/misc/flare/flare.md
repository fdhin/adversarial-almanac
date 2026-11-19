---
name: "flare"
categories:
  - blackarch-misc
version: "0.6"
homepage: "http://www.nowrap.de/flare.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-08-09"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# flare

> Command-line decompiler that recovers ActionScript from Flash SWF files.

## What it's for

Flare (by Igor Kogan, nowrap.de) processes an SWF and extracts every script from it into a single readable text file — useful for recovering lost source, understanding how a Flash component works, or auditing SWF-delivered payloads during malware analysis. Only ActionScript is recovered (no images, sounds or text), there are no command-line options and no GUI, and it cannot modify or recompile SWFs (Flasm covers editing). Unrelated to same-named projects such as the Flare-VM Windows research distribution or backend service frameworks called flare.

## Typical use cases

### Decompile an SWF to readable ActionScript

```
flare banner.swf
```

Outcome: writes `banner.flr` — a plain-text listing of all recovered ActionScript — next to the input file, printing decompilation warnings to the console and exiting silently on success.

*No corpus evidence — derived from upstream documentation.*
