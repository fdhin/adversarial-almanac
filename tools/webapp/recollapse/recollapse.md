---
name: "recollapse"
categories:
  - blackarch-webapp
version: "1.0.r1.g090d711"
homepage: "https://github.com/0xacb/recollapse"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-23"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "0xacb/recollapse"
github-stars: 1385
github-pushed: "2025-08-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# recollapse

> Black-box regex fuzzing helper that mutates an input to bypass validations and reveal normalizations.

## What it's for

Many web filters and WAFs match input with regexes that break on unexpected
bytes: leading control characters, separators around special characters,
Unicode normalizations, case folding or byte truncation. recollapse takes a
single legit input (a URL, email, header, payload) and generates every
variation of it across seven fuzzing modes — start, separators,
normalization, termination, regex metacharacters, case folding and
truncation — in your choice of URL-encoded, Unicode-escaped, raw or
double-URL-encoded output. It only generates payloads; the actual fuzzing is
done by feeding its output to Burp Intruder, Caido, ffuf or a similar tool.
It is also usable as a Python library.

## Typical use cases

### Generate validation-bypass variants of a URL

Fuzz the beginning, separators and end of an input over a byte range.

```
recollapse -e 1 -m 1,2,4 -r 10-11 https://legit.example.com
```

Outcome: prints each variant one per line (`%0ahttps://legit.example.com`,
`https%0a://legit.example.com`, ...); a variant the filter rejects but the
backend accepts is your bypass.

### Pipe mutation fuzzing straight into ffuf

Combine recollapse with a fuzzer to observe which mutations change the
response.

```
echo "<svg/onload=alert(1)>" | recollapse | ffuf -w - -u "https://target.example.com/?param=FUZZ" -mc 200,403,500
```

Outcome: ffuf requests every mutated payload and lists responses with status
200/403/500 — a 200 where the original payload got blocked indicates the
filter was bypassed.

### Generate Unicode-escaped variants for JSON bodies

Use encoding 2 for `application/json` endpoints.

```
echo "a@b.com" | recollapse -e 2
```

Outcome: emits `\u0000a@b.com`, `\u0001a@b.com`, ... suitable for pasting
into JSON request bodies, where raw control bytes would be invalid.

### Dump the normalization, truncation and case tables

The mutation rules come from built-in tables; inspect them as HTML.

```
recollapse -nt --html > normalization_table.html
```

Outcome: writes the full normalization table to an HTML file (similarly
`-tt` / `-ct` for truncation and case tables), documenting which bytes each
mode substitutes.

*No corpus evidence — derived from upstream documentation (0xacb/recollapse README usage, `-h` output and examples).*
