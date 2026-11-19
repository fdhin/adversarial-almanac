---
name: "nbtenum"
categories:
  - blackarch-windows
version: "3.3"
homepage: "http://reedarvin.thearvins.com/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# nbtenum

> Reed Arvin's NetBIOS enumeration utility: queries one host or an IP range for names, users and shares, and audits account passwords along the way.

## What it's for

NBTEnum 3.3 (Reed Arvin) enumerates NetBIOS information from a single host or a range of hosts, with optional password auditing layered on top. In query mode it walks the target's NetBIOS name table under the null user or a supplied account; in attack modes it additionally checks each enumerated user account for weak passwords (blank, password equal to the username in lower case, or every entry in a dictionary file).

The packaged zip ships the Windows console binary `nbtenum.exe` plus its Perl source (`Source/nbtenum.pl` with `iprange.txt` and `dict.txt` samples), so the same tooling runs on both platforms. Targets are given as an IP address or as an input file listing addresses/ranges, which is how whole subnets get swept in one invocation.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Enumerate one host as the null user

```
nbtenum -q 198.51.100.10
```

Outcome: dumps the target's NetBIOS information (names, service types) in the context of the anonymous null session — the minimal, credential-free recon pass against a single Windows host.

### Re-run the query with an account

```
nbtenum -q 198.51.100.10 user ""
```

Outcome: the same enumeration performed under the supplied username (here with a blank password), which surfaces more of the name table and shares when that account holds more browse rights than the null session.

### Sweep a range with dictionary password checks

```
nbtenum -a iprange.txt dict.txt
```

Outcome: enumerates every host listed in `iprange.txt` and tests each discovered user account for blank passwords, username-equals-password and every dictionary entry — a full audit in one pass. Warning from the docs: this can lock out accounts if the target's lockout threshold is above zero.

### Same sweep, lockout-aware

```
nbtenum -s iprange.txt dict.txt
```

Outcome: smart-attack mode performs the same enumeration and password checking but only if the target host's account-lockout threshold is 0, skipping the risky checks otherwise — the safer variant for production ranges.
