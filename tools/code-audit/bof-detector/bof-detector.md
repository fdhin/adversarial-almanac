---
name: "bof-detector"
categories:
  - blackarch-code-audit
version: "19.e08367d"
homepage: "https://github.com/st9140927/BOF_Detector"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "st9140927/BOF_Detector"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# bof-detector

> A Python-based detector of buffer-overflow (BOF) vulnerabilities by source-code-level checking.

## What it's for

bof-detector is a small source-code auditor that flags buffer-overflow-prone constructs in C programs without executing them. Its only home was the GitHub repository `st9140927/BOF_Detector`, which has been deleted: the repository is unreachable, the Wayback Machine holds nothing but a captured 404, and no mirror or surviving documentation of its command-line interface exists. What is verifiable comes from the packaged build: it is a Python 2 program (with an lxml dependency) whose BlackArch wrapper changes into `execution/BOF_analyzer` and runs `main.py`, passing any command-line arguments through. Since no flags or invocation forms can be verified against a surviving source, no use-case examples are documented here; treat the package as historical. For maintained tools covering the same ground, see flawfinder or cppcheck.

*No corpus evidence — description-only note: upstream deleted, no verifiable CLI remains.*
