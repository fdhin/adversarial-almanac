---
name: "dnstwist"
categories:
  - blackarch-scanner
version: "645.3413953"
homepage: "https://github.com/elceef/dnstwist"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elceef/dnstwist"
github-stars: 5777
github-pushed: "2025-04-15"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnstwist

> Permutes a domain name every way a typist or impersonator could, resolves the results in parallel, and reports which lookalikes are registered — optionally flagging those serving a near-copy of your site.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Detecting typosquatting, homograph phishing and brand impersonation against a domain you care about. dnstwist runs a name through its fuzzing algorithms (omitted/duplicated/swapped characters, homoglyphs, vowel and plural changes, subdomain insertion and more), resolves thousands of permutations concurrently, and reports which are actually registered along with their DNS records. For phishing hunting it can also fetch each lookalike's web page and score it against the original using fuzzy hashes (ssdeep or TLSH) or screenshot perceptual hashes (pHash), so a long list of typos collapses into a short list of live impersonations. It does not rate domain reputation or analyze page content beyond similarity, and coverage is deliberately limited to close permutations of the input name.

## Typical use cases

### See only the registered lookalikes

```
dnstwist --registered example.com
```

Outcome: filters the thousands of generated permutations down to those with registered DNS records, printing a table (domain, NS, MX, A) of variants an attacker could actually be using. If your resolver cannot absorb the query volume, point `--nameservers` at a faster resolver or a DNS-over-HTTPS server.

### Widen the search with a wordlist

```
dnstwist --dictionary dictionaries/english.dict example.com
```

Outcome: adds wordlist-derived permutations (keyword prefixes/suffixes such as `example-login.com`) on top of the algorithmic ones before resolving; catches keyword-based typosquats the fuzzers cannot express.

### Check the same name under other TLDs

```
dnstwist --tld dictionaries/common_tlds.dict example.com
```

Outcome: uses the supplied file of TLD strings to test the name under each alternative TLD; the output shows which of those registrations exist, the starting point for takedown or dispute decisions.

### Limit the run to chosen algorithms

```
dnstwist --fuzzers "homoglyph,hyphenation" example.com
```

Outcome: generates permutations from only the named fuzzers instead of all of them, producing a shorter, faster run when one attack class is under investigation.

### Export results for processing

```
dnstwist --format csv example.com | column -t -s,
```

Outcome: emits findings as CSV (piped through `column` here for readable alignment); `--format json` is the equivalent for tooling, and `--format list` prints the bare permutation names without any DNS lookups.

### Flag lookalikes serving a copy of your site

```
dnstwist --lsh example.com
```

Outcome: fetches the web page of every responding permutation, normalizes the HTML and compares its fuzzy hash against the original domain's, reporting each hit with a similarity percentage — high percentages are live phishing clones worth escalating. Add `--phash` to compare rendered screenshots instead (requires a local Chromium), or `--lsh tlsh` to switch hash algorithm.
