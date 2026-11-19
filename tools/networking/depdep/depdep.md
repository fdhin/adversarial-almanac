---
name: "depdep"
categories:
  - blackarch-networking
version: "2.0"
homepage: "https://github.com/galkan/depdep"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "galkan/depdep"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# depdep

> Audits Windows and Unix network shares for sensitive files using configurable regex patterns and Apache Tika text extraction.

*No corpus evidence — derived from upstream documentation.*

## What it's for

DepDep (by galkan) is a compliance-oriented "sentinel" that hunts for sensitive data leaking through a network's file shares. It walks configured SMB/CIFS and Unix shares, extracts text from documents via a locally running Apache Tika server, and flags files whose content matches sensitive-information patterns defined in its XML configuration file — the shipped config includes several ready-made patterns, and auditors can add their own regular expressions (credit-card formats, ID numbers, project codenames). Use it to verify that a company network is not exposing non-compliant data through its shares. It is a config-driven batch auditor, not an interactive scanner: the run profile (targets, patterns) lives in `config/depdep.xml`, and a Tika server plus Java must be running for document parsing. The upstream repository is gone; the packaged release is the 2.0 tarball.

## Typical use cases

### Run a share audit from the config

Scan all configured shares with the configured patterns.

```
depdep --config config/depdep.xml
```

Outcome: mounts/crawls the shares listed in the XML config, extracts document text through Tika, and reports files matching the sensitive-data patterns. By default it resumes from its previous run's progress rather than starting over.

### Start a fresh scan with debug output

Re-scan everything and watch what it is doing.

```
depdep --config config/depdep.xml -w 1 -v 1
```

Outcome: `-w 1` discards the saved progress so the scan starts from scratch, and `-v 1` enables verbose/debug messages — the combination to use when validating a modified config or diagnosing why a share is skipped.
