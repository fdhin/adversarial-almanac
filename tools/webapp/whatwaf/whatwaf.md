---
name: "whatwaf"
categories:
  - blackarch-webapp
version: "392.b14e866"
homepage: "https://github.com/Ekultek/WhatWaf"
packaged-by: [blackarch]
blackarch-builddate: "2022-06-04"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "Ekultek/WhatWaf"
github-stars: 2935
github-pushed: "2024-08-11"
github-archived: false
github-issues: 478
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whatwaf

> Detect the web application firewall in front of a site and attempt tamper-script bypasses for it.

## What it's for

WhatWaf fingerprints the web application firewall or protection system deployed in front of a target, then attempts to derive a bypass using its library of tamper scripts — payload transformations such as base64 encoding, apostrophe masking or null-byte appending, comment splicing. Reach for it before pointing a scanner or exploit tool at a target whose responses look filtered, to learn both that a WAF is present and which of its rules might be circumvented. It performs detection and bypass crafting only; it does not run exploitation itself.

## Typical use cases

### Detect the WAF protecting a target

```
whatwaf -u https://target.example.com
```

Outcome: fingerprints the target and reports which protection system sits in front of it (e.g. Cloudflare, ModSecurity, F5 BIG-IP), along with any tamper-script bypasses it could derive for that firewall.

### Check many targets at once

```
whatwaf -l urls.txt
```

Outcome: runs the detection against every URL in the file, reporting each target's protection status in sequence.

### List every WAF it can identify

```
whatwaf --wafs
```

Outcome: prints the catalog of detectable protection systems (86 in the current version) so coverage can be checked before relying on the tool.

### List available tamper scripts

```
whatwaf --tampers
```

Outcome: prints each tamper's load path and description (e.g. `apostrephemask`, `base64encode`, `appendnull`) for use in crafting bypass payloads.

*No corpus evidence — derived from upstream documentation (README and feature list).*
