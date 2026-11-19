---
name: "pencode"
categories:
  - blackarch-misc
version: "39.07e33ef"
homepage: "https://github.com/ffuf/pencode"
packaged-by: [blackarch]
blackarch-builddate: "2024-01-20"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "ffuf/pencode"
github-stars: 246
github-pushed: "2024-01-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pencode

> Chains multiple encodings, decodings and hashes over text piped in on stdin.

## What it's for

pencode applies a sequence of text transformations — Base64, hex, URL encoding (reserved characters or all characters), UTF-16 (LE/BE), HTML/XML/JSON escaping, Unicode escaping, MD5/SHA checksums, case changes — to data from stdin or a wordlist file, executing each stage in the order given on the command line. It was built for payload-crafting automation: producing the multiply-encoded variant of an injection string in one pipe instead of six round-trips through separate tools, or transforming an entire wordlist before feeding a fuzzer. It is a plain Unix filter: no interactivity, output goes to stdout.

## Typical use cases

### Apply an encoding chain to a string

Compose several encodings in a single invocation.

```
echo 'what%ever' | pencode urlencode b64encode hexencode
```

Outcome: prints the input after URL-encoding, then Base64, then hex — a single line such as `64326868644355794e5756325a58493d`, ready to paste into a request.

### Encode a whole wordlist line by line

Prepare every entry of a wordlist for a fuzzer that expects encoded input.

```
pencode -input wordlist.txt b64encode
```

Outcome: reads the file one line at a time and prints the Base64 form of each line, preserving line structure.

### List the available transformations

Discover the exact names usable in a chain.

```
pencode -list
```

Outcome: prints every encoder, decoder and hash name (b64encode, urlencodeall, utf16be, sha256, ...).

### Drop the payload into a request template

Wrap the transformed payload in a larger file.

```
echo 'injection-string' | pencode urlencode request.tmpl
```

Outcome: reads `request.tmpl`, replaces every `#PAYLOAD#` marker with the URL-encoded input, and prints the finished file — any argument ending in `.tmpl` is treated as a template this way.

*No corpus evidence — derived from upstream documentation.*
