---
name: "samesame"
categories:
  - blackarch-fuzzer
version: "68.a9bcd7b"
homepage: "https://github.com/TheTarquin/samesame"
packaged-by: [blackarch]
blackarch-builddate: "2021-08-08"
build-system: go
license: custom:IDGAD
meta-checked: "2026-11-09"
github-repo: "TheTarquin/samesame"
github-stars: 37
github-pushed: "2021-08-05"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# samesame

> Command-line generator of homograph strings: replaces ASCII characters with visually identical Unicode look-alikes.

## What it's for

samesame (TheTarquin, written in Rust) transforms text by swapping ASCII characters for Unicode homoglyphs — characters that render the same to a human eye but differ at the byte level. Its purpose is testing how people and systems handle look-alike text: whether a mail gateway, proxy filter, DLP rule, or log alerting still matches keywords after homograph substitution, whether code review or chat tools render disguised strings identically, and building realistic phishing-simulation payloads. It accepts a string argument or stdin, and offers toggles for digit substitution, ligatures, Unicode whitespace, and invisible word joiners. It is purely a local text transform with no network functionality. (Not to be confused with SensePost's unrelated egress-testing tool of the same name.)

## Typical use cases

### Homograph a string

Produce a visually identical, byte-different version of a phrase.

```
samesame "Invoice Approved"
```

Outcome: prints the input with letters replaced by look-alike Unicode characters — a human reads the same words, while exact string matching against the original no longer fires; use it to build test cases for detection rules and filters.

### Pipe text through it

Transform program output inside a pipeline.

```
echo "wire transfer authorization" | samesame
```

Outcome: reads stdin, writes the homograph version to stdout, so generators of mail templates, ticket text, or chat messages can be piped through it during phishing simulations or filter tests.

### Tune the substitution depth

Escalate past simple letter swaps.

```
samesame -n -l -w "Vault 42 Access"
```

Outcome: `-n` also substitutes look-alike digits (`4` → `４`), `-l` replaces character pairs with equivalent ligatures, and `-w` swaps spaces for Unicode whitespace variants — progressively less of the original ASCII survives, useful for finding the point where a filter's normalization gives up.

### Insert invisible word joiners from a file

Break naive matching without changing visible text at all.

```
samesame -z -i payload.txt -o out.txt
```

Outcome: inserts invisible word-joiner characters (U+2060) between the characters of `payload.txt` and writes the result to `out.txt` — the text renders exactly as before, but substring and keyword matches against the plain form fail.

*No corpus evidence — derived from upstream documentation.*
