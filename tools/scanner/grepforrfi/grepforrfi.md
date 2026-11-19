---
name: "grepforrfi"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://www.irongeek.com/downloads/grepforrfi.txt"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/grepforrfi"
github-stars: 0
github-pushed: "2019-03-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grepforrfi

> Irongeek's single-file log parser: mines web-server access logs for RFI payloads and webshell traffic and archives what it finds.

## What it's for

grepforrfi (Irongeek, v1.2) is a Python 2 script that post-processes Apache-style access logs:
it extracts request URLs that look like remote-file-inclusion attempts (`http://` URLs injected
into page parameters), checks which payload URLs are still alive, records unique and dead RFI
links, and saves copies of reachable webshells, writing out an HTML report page alongside the
text lists. It is defensive/forensic — it explains what already hit your logs, and never touches
any host beyond liveness-checking the payload URLs it found. Input and output paths are not
command-line arguments: you edit the "Main setting" block at the top of the script (log paths,
output files, capture directory) before running it.

## Typical use cases

### Mine a log for RFI and webshell traffic

After pointing the script's Main setting block at your access logs and desired outputs, run it.

```
sudo grepforrfi -d
```

Outcome: parses the configured logs, printing suspected RFI/webshell lines as they match (`-d`
adds debug detail), tests each payload URL for liveness and writes the configured outputs: the
unique-RFI list, the dead-RFI list, captured webshell copies, and the HTML report page.

*No corpus evidence — derived from upstream source (grepforrfi v1.2 read from
irongeek.com/downloads/grepforrfi.txt; the Seabreg GitHub repo in the frontmatter is a
byte-identical mirror of the same script).*
