---
name: "enum4linux-ng"
categories:
  - blackarch-recon
version: "454.288826f"
homepage: "https://github.com/cddmp/enum4linux-ng"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-02-21"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cddmp/enum4linux-ng"
github-stars: 1687
github-pushed: "2026-02-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# enum4linux-ng

> Python 3 rewrite of enum4linux that parses Samba-tool output cleanly and exports Windows/Samba enumeration results as YAML or JSON.

## What it's for

enum4linux-ng is a next-generation rewrite of Mark Lowe's enum4linux.pl (Portcullis/Cisco CX Security Labs) for enumerating information from Windows and Samba systems. Like the original it wraps `nmblookup`, `net`, `rpcclient` and `smbclient`, but it parses all of their output and can export every finding as YAML or JSON for further processing by other tools. It natively implements ldapsearch and polenum functionality, supports multiple authentication methods (password, Kerberos ticket via `-K`, NTLM hash via `-H`), legacy SMBv1, timeout handling and IPv6, and its "smart" enumeration disables tests that would fail anyway (e.g. skipping LDAP checks when port 389 is closed). Parameter naming differs from the original: `-A` replaces enum4linux's `-a` for simple enumeration, group members need `-Gm`, and RID cycling is opt-in via `-R`.

## Typical use cases

### Smart enumeration with YAML export

Run the default enumeration set and export findings.

```
enum4linux-ng.py -As 198.51.100.10 -oY out
```

Outcome: performs simple enumeration (`-A`) with smart skipping (`-s`-style auto-disable of failing tests), printing parsed results to the console and writing everything to `out.yaml` for downstream tooling.

### Authenticated enumeration

Enumerate with explicit credentials and structured output.

```
enum4linux-ng.py 198.51.100.10 -u user -p Password1 -oY out
```

Outcome: with no enumeration option given, `-A` is assumed; the authenticated session returns fuller user/share/group data than anonymous, exported to `out.yaml`. Use `-oJ out.json` for JSON instead.

### Enumerate plus service listing

Add service enumeration on top of the default set.

```
enum4linux-ng.py 198.51.100.10 -A -C
```

Outcome: runs simple enumeration plus `-C` service enumeration, auto-detecting requirements like SMBv1 along the way; nothing is written to file, results go to the console.

### Enable RID cycling

Turn on the non-default RID brute-force to enumerate users via SID lookups.

```
enum4linux-ng.py 198.51.100.10 -R
```

Outcome: performs RID cycling (faster than the original by grouping multiple SID lookups per rpcclient call), enumerating users/groups even where list APIs are restricted.

No corpus evidence — derived from upstream documentation.
