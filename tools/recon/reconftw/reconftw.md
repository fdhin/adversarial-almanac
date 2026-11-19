---
name: "reconftw"
categories:
  - recon
version: "git (2026-11-02)"
homepage: "https://github.com/six2dez/reconftw"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "six2dez/reconftw"
github-stars: 8350
github-pushed: "2026-11-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 276
gh-activity-checked: "2026-11-09"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 5
---

# reconftw

> Bash orchestrator that runs a whole-domain recon pipeline — OSINT, subdomains, hosts, webs, vulns — by chaining dozens of external tools.

## What it's for

reconFTW automates reconnaissance on a target domain by orchestrating a large toolbox of external programs (subfinder, puredns, httpx, nuclei, nmap, ffuf, gitleaks and many more) through the phases of a standard methodology: OSINT and leaks, subdomain enumeration (passive, permutations, bruteforce), host and port analysis, web probing and fuzzing, vulnerability scanning, and reporting. Behavior is controlled end-to-end by `reconftw.cfg` (module toggles, tool paths, API keys, rate limits, notification targets), and results land in an organized per-target output tree. It is an aggressive, internet-facing scanner — run it only against scopes you are authorized to test, and prefer a VPS for the deep modes. A Go rewrite exists as an opt-in pre-release binary named `reconftw`; the bash entry point remains `reconftw.sh`.

## Typical use cases

### Full recon on one domain

```
./reconftw.sh -d example.com -r
```

Outcome: runs the complete default pipeline — OSINT, subdomain enumeration, resolution, web probing, content discovery and vulnerability scanning — and writes the structured results and report under the output directory for that domain.

### Recon a list of targets

```
./reconftw.sh -l targets.txt -r -o ./results/
```

Outcome: iterates the same pipeline over every domain in `targets.txt`, storing each target's findings in its own folder under `./results/`.

### Deep mode for exhaustive coverage

```
./reconftw.sh -d example.com -r --deep
```

Outcome: enables the heavier checks (deeper scans and longer wordlists); significantly slower and resource-hungry, intended for a VPS rather than a laptop.

### Include active attacks (YOLO mode)

```
./reconftw.sh -d example.com -a
```

Outcome: on top of recon, runs the exploitation-oriented modules such as password spraying against discovered services — only for targets where active testing is authorized.

### Monitor a domain over time

```
./reconftw.sh -d example.com -r --monitor --monitor-interval 30 --monitor-cycles 48
```

Outcome: repeats the scan every 30 minutes for 48 cycles, diffing findings against previous runs so new assets, subdomains or exposures surface as they appear; `--report-only --export all` later regenerates the report artifacts (JSON/HTML/CSV) without rescanning.

*No corpus evidence — derived from upstream documentation.*
