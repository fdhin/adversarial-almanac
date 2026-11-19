---
name: "remot3d"
categories:
  - blackarch-webapp
version: "38.a707ef7"
homepage: "https://github.com/KeepWannBe/Remot3d"
packaged-by: [blackarch]
blackarch-builddate: "2020-10-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "KeepWannBe/Remot3d"
github-stars: 283
github-pushed: "2020-10-02"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-07"
github-status: "gone"
---

# remot3d

> Menu-driven generator and remote controller for PHP backdoors, with disabled-function and read-file bypass helpers.

## What it's for

Remot3d is an interactive bash script for post-exploitation on hosts that run
PHP. From a numbered menu it generates PHP backdoor files for Linux or
Windows targets, then lets you operate the deployed backdoor remotely from
the same tool. It bundles bypass helpers for common hardening: exploiting the
`imap_open` vulnerability to escape `disabled_functions`, and alternative
scripts (cURL-based and "unique logic" PHP files) to read files such as
`/etc/passwd` when normal file reads are blocked. It is a 2020-era script
with no maintenance since; modern PHP versions have fixed the underlying
`imap_open` trick, so treat it as a lab tool and expect the bypasses to fail
on current stacks.

## Typical use cases

### Launch the backdoor menu

Start the interactive controller after installing the package.

```
remot3d
```

Outcome: opens the numbered menu where you pick backdoor generation (Linux
or Windows PHP payload), connect to an already-deployed backdoor, or use the
bypass modules; generated payloads are written to the current directory for
upload to the target.

*No corpus evidence — derived from upstream documentation (Remot3d README and Remot3d.sh, verified via the makiraid/Remot3d mirror: the original KeepWannBe repo returns 404).*
