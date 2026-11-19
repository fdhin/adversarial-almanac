---
name: "sunpatchcheck"
gentoo-category: "app-admin"
version: "3.3"
homepage: "http://www.titania.co.uk/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sunpatchcheck

> Compares Solaris `showrev -p` output against Sun's recommended patch list.

## What it's for

Sun Patch Check (Ian Ventura-Whiting of Titania — the Nipper and original
SSLScan author; GPL v2) is a small C utility for auditing patch levels on
legacy Solaris hosts. It keeps a database of Sun's recommended patch clusters
per Solaris version (10, 9, 8, 7, 2.6 and 2.5.1; append `i` for the x86
variant, e.g. `10i`), built or refreshed with `--update`, then compares the
patch inventory captured from `showrev -p` on a target against that database
and lists the recommended patches that are not installed. It dates from
2007–2008 (v1.1 documented on the author's page, release 3.3 packaged in
Pentoo), so reach for it when assessing long-lived Solaris estates, not
modern infrastructure.

## Typical use cases

### Build the recommended-patch database

Required before the first audit.

```
sunpatchcheck --update
```

Outcome: the recommended-patch databases for the supported Solaris versions
are created or refreshed locally, enabling subsequent checks.

### Audit a host's patch level

Diff a captured inventory against the database.

```
sunpatchcheck --check=showrev.txt --solaris=10
```

Outcome: showrev.txt (the output of `showrev -p` run on the target host) is
compared against the Solaris 10 recommended-patch list and the missing
recommended patches are printed — the patch-gap report for that host.

*No corpus evidence — derived from the archived upstream project page
(titania.co.uk/sunpatchcheck.php, 2007 snapshot; v1.1 documented there,
packaged release 3.3).*
