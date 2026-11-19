---
name: "sourceleakhacker"
categories:
  - recon
version: "git (2026-03-26)"
homepage: "https://github.com/wangyihang/sourceleakhacker"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "wangyihang/sourceleakhacker"
github-stars: 391
github-pushed: "2026-03-26"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 4
---

# sourceleakhacker

> Multi-threaded web scanner that probes a site with a built-in dictionary of sensitive paths to find exposed source and backup files.

## What it's for

Finding source-code leaks on web applications. It walks a built-in dictionary of directory and file names where leaks typically live (admin panels, `index.php` variants, `.bak`/`.back` copies), records the status code, size, response time and content type of every response, and saves the full result set to a timestamped CSV. `--scale tiny` gives a fast pass and `--scale full` the complete dictionary; thread count, per-request timeout and log level are tunable. It discovers exposed content only — it does not test for vulnerabilities.

## Typical use cases

### Scan one site

```
python3 SourceLeakHacker.py --url http://target.example.com/ --threads 4 --timeout 8
```

Outcome: live output lines in the form `[status] size time content-type URL` for each probed path, ending with `Result save in file: result/<timestamp>.csv`.

### Scan a list of sites

```
python3 SourceLeakHacker.py --urls urls.txt --threads 8
```

Outcome: runs the same dictionary sweep against every URL in the file (one per line), logging hits for all sites into one CSV.

### Full dictionary into a chosen folder

```
python3 SourceLeakHacker.py --url http://target.example.com/ --scale full --output leaks
```

Outcome: uses the complete built-in wordlist rather than the fast subset and writes the resulting CSV under `leaks/` instead of the default timestamped `result/` folder.

*No corpus evidence — derived from upstream documentation (wangyihang/SourceLeakHacker README).*
