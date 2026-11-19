---
name: "rusthound"
categories:
  - blackarch-recon
version: "1.1.69.r4.g241751c"
homepage: "https://github.com/NH-RED-TEAM/RustHound"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-15"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "NH-RED-TEAM/RustHound"
github-stars: 1183
github-pushed: "2024-10-21"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rusthound

> Original Rust BloodHound collector — the legacy variant, kept for BloodHound 4.x ingest only.

## What it's for

RustHound is the original Rust rewrite of SharpHound (NH-RED-TEAM / NeverHack), the direct ancestor of [[rusthound-ce]]. Its own README marks it as compatible with BloodHound Legacy 4.x only and sends current BloodHound Community Edition users to RustHound-CE, which is the actively developed branch by the same author and carries the full reference for this binary and flag family — collection there is the same `rusthound-ce -d DOMAIN -u user -z` shape. Keep this variant only when the ingest target is a legacy BloodHound 4.x instance; its FQDN-resolver and ADCS collector modules produce the v4-format JSON those instances require.

*Pointer note — canonical reference: [[rusthound-ce]]. No corpus evidence — derived from upstream documentation.*
