---
name: "pev"
categories:
  - blackarch-forensic
version: "0.81"
homepage: "http://pev.sourceforge.net/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-02-02"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mentebinaria/readpe"
github-stars: 2803
github-pushed: "2026-10-08"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# pev

> Original name of the PE-analysis toolkit now published as readpe — canonical note: [[readpe]].

pev ("PE Version") was the SourceForge-era name of the PE32/PE32+ analysis toolkit by Fernando Mercês and Mente Binária. The project moved to GitHub and was renamed readpe (with libpe absorbed into the same repository), because the toolkit had outgrown the original name; the docs still live under pev.sourceforge.io. BlackArch and nixpkgs keep packaging the final SourceForge release (0.81) under the old `pev` name, while Kali and AUR ship the renamed package as `readpe`. It is one tool, not two: the full reference with use cases lives in [[readpe]].
