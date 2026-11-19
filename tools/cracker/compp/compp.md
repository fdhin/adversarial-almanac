---
name: "compp"
categories:
  - blackarch-cracker
version: "1.0.5"
homepage: "https://github.com/sec-it/ComPP"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "sec-it/ComPP"
github-stars: 64
github-pushed: "2022-02-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# compp

> Builds company-targeted bruteforce wordlists from names, locations, and keywords, with case/year/symbol transformations.

## What it's for

ComPP (Company Passwords Profiler, by SEC-IT) generates password-spraying wordlists from a handful of organization facts: company/application names, ZIP codes, city names, and free keywords. It expands those fields through case variants (lowercase, UPPERCASE, Capitalized), field permutations (default up to 2 fields per password), a range of years, and a range of special-character suffixes — producing candidates like `Company75000!` or `APPNAME2019#`. Optionally it appends numbers and applies l33t substitution. Input comes from an interactive prompt or a JSON file, and output goes to stdout or a file. It is a wordlist generator, not a cracker — pair its output with whatever remote brute-force or spraying tool you run.

## Typical use cases

### Generate a wordlist interactively
```
compp
```
Outcome: prompts for comma-separated company/application names, ZIP codes, cities, and keywords (unknown fields can be left blank), then prints the generated wordlist to stdout with candidates such as `ParisCompany2021_` and `appname1995+`.

### Generate from a JSON profile
```
compp corp.json -o wordlist.txt
```
Outcome: reads the profile — `{"company": [...], "zip_codes": [...], "cities": [...], "keywords": [...]}` — and writes the full candidate list to `wordlist.txt` instead of stdout, ready to feed a spraying tool.

### Add number and l33t transformations
```
compp corp.json -l -n -o wordlist.txt
```
Outcome: extends the base wordlist with number-suffixed and 1337-speak variants (`-l`, `-n`), multiplying the candidate count for targets whose complexity rules would otherwise filter the plain combinations out.

### Control permutation depth
```
compp corp.json -p 3 -o wordlist.txt
```
Outcome: allows combinations of up to 3 fields per password instead of the default 2, trading a larger wordlist for coverage of longer passphrase-style passwords.

*No corpus evidence — derived from upstream documentation.*
