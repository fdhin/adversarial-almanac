---
name: "tnscmd"
categories:
  - blackarch-misc
version: "1.3"
homepage: "http://www.red-database-security.com/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tnscmd

> Same tool as tnscmd10g under its BlackArch name — see the tnscmd10g note.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This package and [[tnscmd10g]] are one tool, not two. BlackArch's `tnscmd`
recipe downloads the tnscmd10g Perl script from red-database-security.com
(jwa's original tnscmd extended for Oracle 10g), installs it as
`/usr/bin/tnscmd`, and adds a `/usr/bin/tnscmd10g` symlink — the recipe even
declares `replaces=('tnscmd10g')`. The original author's homepage on
jammed.com is gone (404). Everything the tool does — issuing ping, version,
status, services and raw TNS commands against an Oracle tnslsnr on 1521/tcp
without an Oracle client — is documented with examples in the
[[tnscmd10g]] note.
