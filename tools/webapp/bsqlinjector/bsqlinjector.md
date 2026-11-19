---
name: "bsqlinjector"
categories:
  - blackarch-webapp
version: "14.98c8ce8"
homepage: "https://github.com/enjoiz/BSQLinjector"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "enjoiz/BSQLinjector"
github-stars: 101
github-pushed: "2024-12-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bsqlinjector

> Ruby tool that extracts data through blind SQL injection from a saved HTTP request.

## What it's for

BSQLinjector exploits blind SQL injection by replaying a raw HTTP request you captured beforehand: the request file contains the full vulnerable request with the word `SQLINJECT` marking where payloads go, and a `--pattern` string that only appears when the injected condition is true. It then enumerates data character by character, with four search strategies trading request count for completeness (`between`, `moreless`, `like`, `equals`), hex-mode comparison, case sensitivity, and PostgreSQL `for`-style substring handling. It works for first- and second-order injection (via `--2ndfile`) and runs through a proxy for burp-style workflows. Written in Ruby; the request-and-pattern model means it fits any HTTP context a request can be captured from.

## Typical use cases

### Enumerate data through a captured request

Provide the saved request, the true-marker pattern, and the payload scaffolding.

```
bsqlinjector --pattern=truestatement --file=req.txt --schar="'" --prepend="abcd'and'a'='b'+union+select+'truestatement'+from+table+where+col%3d'value'+and+substr(password," --append="'#" --ssl
```

Outcome: `req.txt` holds the raw HTTP request with `SQLINJECT` at the injection point; the tool walks the target string character by character (default mode between/`b`), printing each recovered character, and finishes with the extracted value such as a password hash column.

### Dry-run the payload before sending anything

Preview exactly what will be injected.

```
bsqlinjector --pattern=truestatement --file=req.txt --prepend="...'and+substr(password," --append="')" --test
```

Outcome: `--test` prints the fully assembled payload for the configured options without sending a single request — use it to confirm quoting, hex mode (`--hex`), bracket placement (`--bracket`) and encoding before running the real enumeration.

Tuning knobs worth knowing: `--mode` picks the inference strategy (b/a/l/e), `--postgres` switches the substring syntax for PostgreSQL, `--start`/`--max` bound the enumeration, and `--proxy=127.0.0.1:8080` mirrors traffic into an intercepting proxy.

No corpus evidence — derived from upstream documentation.
