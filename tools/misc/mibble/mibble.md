---
name: "mibble"
categories:
  - blackarch-misc
version: "2.10.1"
homepage: "http://www.mibble.org/"
packaged-by: [blackarch]
blackarch-builddate: "2017-06-04"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "cederberg/mibble"
github-stars: 130
github-pushed: "2022-04-28"
github-archived: false
github-issues: 13
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mibble

> Java SNMP MIB parser library with bundled IANA/IETF MIBs, plus a validator, a pretty-printer and a MIB browser GUI.

## What it's for

Mibble (Per Cederberg) is an open-source SNMP MIB parser (SMIv1 and SMIv2) library for Java: it reads MIB files in ASN.1 syntax and exposes OIDs, types, descriptions, comments and the original MIB source text through an object API. All standard IANA and IETF MIBs are bundled, and imports resolve automatically from the bundle, the filesystem or URLs; parse errors are reported with details and an excerpt of the offending source. Besides the library it ships three small applications: a syntax validator, a pretty-printer that reformats MIB files, and a graphical MIB browser with a simple SNMP manager for reading values from a live device. Reach for it when a vendor hands you an opaque `.mib`/`.my` file and you need to know whether it parses, what it defines, or which OID a name maps to. It is dual-licensed under the GNU GPL (v2) or a commercial license.

## Typical use cases

### Validate a vendor MIB

Check a downloaded MIB for syntax errors.

```
mibblevalidator vendor.mib
```

Outcome: the file is parsed with its imports resolved from the bundled standard MIBs; errors and warnings print with line references and a short excerpt of the problematic text. Several files can be checked in one run.

### Pretty-print a MIB

Normalize formatting so differences between revisions become readable.

```
mibbleprinter vendor.mib > vendor.formatted.mib
```

Outcome: the reformatted MIB text is written to stdout and, with the redirect, saved as a new file; pass multiple MIB files to normalize them in one run.

### Browse MIBs and query a live device

Open the GUI browser.

```
mibblebrowser
```

Outcome: a Swing window opens where you load MIB files, navigate their OID trees with types and descriptions, and — through the built-in SNMP manager — get or walk values from a live SNMP agent to match definitions against what the device actually answers.

*No corpus evidence — derived from upstream documentation.*
