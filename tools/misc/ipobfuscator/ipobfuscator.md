---
name: "ipobfuscator"
categories:
  - blackarch-misc
version: "27.f005262"
homepage: "https://github.com/OsandaMalith/IPObfuscator"
packaged-by: [blackarch]
blackarch-builddate: "2022-07-31"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OsandaMalith/IPObfuscator"
github-stars: 145
github-pushed: "2022-07-29"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipobfuscator

> Interactive converter that rewrites an IPv4 address into ~20 alternative URL notations (DWORD, dotted-hex, dotted-octal, and mixed forms).

## What it's for

IPObfuscator (Osanda Malith) is a tiny C program with one job: you type an IPv4 address and it prints the many ways that address can be written in a URL — the 32-bit decimal DWORD form (`http://3735397211`), dotted hexadecimal (`0xDE.0xA5.0xA3.0x5B`), dotted octal, and mixed combinations of all three. Browsers and many parsers resolve these to the same host, while anything matching on the plain dotted-quad form — log review, string filters, blocklists — will miss them, which makes these notations a standard trick for testing whether a WAF or monitor normalizes IP representations. It converts one address at a time; it does not scan, connect, or fetch anything.

## Typical use cases

### Convert an address interactively

```
ipobfuscator
```

Outcome: prompts `Enter your IP:`; typing `198.51.100.10` prints roughly twenty equivalent `http://...` forms (DWORD, hex, octal, and mixed) ready to paste into a browser or filter test. An invalid input prints `Enter a valid ip`.

### Convert from a script

```
echo 198.51.100.10 | ipobfuscator
```

Outcome: reads the address from stdin instead of waiting on the prompt and prints the same set of obfuscated URL forms, so conversions can be batched in shell pipelines.

*No corpus evidence — derived from upstream documentation.*
