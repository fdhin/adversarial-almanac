---
name: "unisec"
categories:
  - blackarch-misc
version: "0.0.10"
homepage: "https://noraj.github.io/unisec"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-07-04"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "Acceis/unisec"
github-stars: 39
github-pushed: "2024-10-07"
github-archived: false
github-issues: 7
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unisec

> Unicode security toolkit: confusables, BiDi spoofing, normalization, encodings.

*No corpus evidence — derived from upstream documentation.*

## What it's for

unisec (Alexandre ZANNI / noraj, ACCEIS; a Ruby gem) is a CLI for the
Unicode tricks that matter in spoofing and web-attack testing: homoglyph
and confusable substitution, BiDi (right-to-left override) payload crafting
for fake domain names or file names, NFC/NFD/NFKC/NFKD normalization and
HTML-escape bypass for XSS, UTF-8/16/32 hexdumps, code-point properties,
surrogate conversion and character/grapheme sizes. Reach for it to craft or
dissect a suspicious string — it is offline analysis of strings you pass
in, not a live-target tester.

## Typical use cases

### Craft a BiDi spoofing payload

Make a displayed string read differently than it sorts.

```
unisec bidi spoof example.com
```

Outcome: returns the input wrapped in BiDi control characters (RLO/PDF by
default) so the rendered text appears reversed — the classic
file-extension and domain spoof; a light-display option prints only the
payload itself for piping.

### Replace characters with random confusables

Generate homoglyph-spoofed variants of a string.

```
unisec confusables randomize example.com
```

Outcome: replaces each character with a random confusable where one exists,
printing a visually near-identical string built from different code points
— useful when testing IDN and brand-protection controls. `confusables
list <char>` prints the candidates for a single character.

### Compare all normalization forms

See how a payload behaves under each Unicode normalization form.

```
unisec normalize all "<a href=javascript:alert(1)>x</a>"
```

Outcome: prints the NFC, NFD, NFKC and NFKD forms of the input side by
side — compatibility forms (NFKC/NFKD) collapsing characters is what makes
full-width and look-alike WAF bypasses work.

### Prepare an HTML-escape-bypass payload

Check whether escaping survives normalization.

```
unisec normalize replace "<img src=x onerror=alert(1)>"
```

Outcome: prints the string HTML-escaped and then normalized (NFKC/NFKD) —
a form that can slip past naive escaping filters because normalization
re-materializes the original characters after the escape step.

### Hexdump a string in every Unicode encoding

Inspect the exact bytes behind a string.

```
unisec hexdump "café"
```

Outcome: prints UTF-8, UTF-16 and UTF-32 hexadecimal dumps of the input —
the encoding mismatch a spoof relies on becomes visible; pass `-` to read
from stdin, or restrict output to one encoding with `--enc`.
