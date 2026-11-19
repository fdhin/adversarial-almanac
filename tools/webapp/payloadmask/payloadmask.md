---
name: "payloadmask"
categories:
  - blackarch-webapp
version: "17.58e0525"
homepage: "https://github.com/CoolerVoid/payloadmask"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-20"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "CoolerVoid/payloadmask"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# payloadmask

> C utility that rewrites web payload lists into WAF-bypass variants.

## What it's for

PayloadMask edits lists of web attack payloads, applying transformations ("tampering") that produce masked variants intended to slip past web application firewalls — the idea behind sqlmap's tamper scripts, applied offline to a payload wordlist. It is a small C program (built with `make`, installed as `payloadmask`) whose source layout (`tamper.c`, `string_ops.c`, `file_ops.c`) reflects the pipeline. The package ships payload collections under `/usr/share/payloadmask/payloads` and documentation under `/usr/share/doc/payloadmask`. The GitHub repository has been deleted and no verifiable interface documentation survives, so no command examples are given here; consult the packaged docs for the actual flags.

No corpus evidence — description derived from the archived README and the package recipe; CLI unverifiable.
