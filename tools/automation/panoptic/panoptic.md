---
name: "panoptic"
categories:
  - blackarch-automation
version: "262.e138360"
homepage: "https://github.com/lightos/Panoptic"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "lightos/Panoptic"
github-stars: 325
github-pushed: "2026-11-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 60
gh-activity-checked: "2026-11-09"
---

# panoptic

> LFI content harvester: point it at one path-traversal-vulnerable parameter and it concurrently probes thousands of common log/config file paths, filters the hits with response heuristics, and dumps the retrieved files.

## What it's for

Turning a single local-file-inclusion (path traversal) finding into full file retrieval at scale. Panoptic knows where the interesting files live on Linux and Windows — service configs, logs, credentials — and sprays those paths through whichever injection channel you have: a GET/POST parameter, the URL path itself, cookies, arbitrary headers, or JSON bodies (a `FUZZ` marker names the injection point anywhere). It then separates real reads from error pages using heuristic comparison that ignores CSRF tokens, nonces, timestamps and reflected payloads, auto-detects the target OS to narrow the dictionary, chains discoveries (parses `/etc/passwd` for home-directory file paths, `mysql-bin.index` for binlogs), and offers filter-bypass prefixes (`....//`, double-encoded `..%252f`) for WAF'd endpoints. Output goes to console/JSON/CSV with resume support, or files on disk. It does not exploit anything beyond reading files — no RCE, no deserialization chains — and needs you to hand it a working injection point first. Actively maintained, Python 3.11+.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Harvest config files through a vulnerable parameter

```
panoptic --url "http://target.example.com/include.php?file=test.txt" --write-files --output-dir loot
```

Outcome: concurrently probes the parameter with its dictionary of common log/config paths, prints each confirmed readable file, and saves every retrieved file under `loot/` (default `./output`) with the page template stripped where possible — credentials in config files are the usual payoff. Add `--param file` when the URL has several parameters, or `--data "file=test.txt&id=1" --param file` to test a POST body instead.

### Inject through cookies, headers or JSON bodies

Place the `FUZZ` marker wherever the payload must land:

```
panoptic --url "http://target.example.com/api/load" --data '{"file":"FUZZ"}' --auto
```

Outcome: the traversal dictionary is substituted into the marked position of the JSON body (`--header "Cookie: lang=FUZZ"` and `--header "X-Template: FUZZ"` work the same way for cookies and arbitrary headers), with `--auto` letting Panoptic detect OS and target type on its own. This reaches injection points that plain URL-parameter testing misses.

### Bypass a filter with encoded traversal prefixes

```
panoptic --url "http://target.example.com/include.php?file=test.txt" --prefix "..%252f" --multiplier 6
```

Outcome: prefixes are sent verbatim — here only the slash is double-encoded, so a filter that decodes once sees no `../` while the application's second decode yields the real path; `--multiplier 6` repeats the prefix six times to climb out of deep directories. `--prefix "....//....//"` is the alternative for filters that strip `../` literally, and `--path-based --prefix "../" --multiplier 6` applies the same idea to path-based LFI where the traversal replaces the last URL segment.

### Scope the scan by OS or software, and checkpoint it

```
panoptic --url "http://target.example.com/include.php?file=test.txt" --os "*NIX" --software PostgreSQL --output-format json --output-file results.json --resume-file scan.checkpoint
```

Outcome: restricts the dictionary to Unix-flavored paths for PostgreSQL specifically (run without `--software`/`--os` to see all available filter values), writes structured results to `results.json`, and periodically records progress in `scan.checkpoint` — re-running the same command resumes an interrupted long scan instead of restarting it.
