---
name: "aquatone"
categories:
  - blackarch-recon
version: "v1.9.1.shelld3v.r14.g30fbf6e"
homepage: "https://github.com/shelld3v/aquatone"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-02-03"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "shelld3v/aquatone"
github-stars: 141
github-pushed: "2024-11-20"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aquatone

> Fly over a large host list and get a screenshot-clustered HTML report of its HTTP attack surface.

## What it's for

Aquatone takes URLs, domains and IP addresses (piped in from any host-discovery tool — it extracts them with regex) and probes typical web ports, fetches response headers and bodies, screenshots every responsive page with headless Chrome/Chromium, and clusters the screenshots by visual similarity. The result is a single HTML report for quickly reviewing which of hundreds of hosts actually run a web application. This is the actively maintained shelld3v fork of the original (discontinued) michenriksen project. It does not find hosts itself and is not a vulnerability scanner — it needs a host list and a Chrome/Chromium installation.

## Typical use cases

### Screenshot a host list into a report

Pipe any host list into aquatone and let it probe the default web ports.

```
cat hosts.txt | aquatone -out reports/example.com
```

Outcome: writes `aquatone_report.html` (screenshots and headers clustered by similarity), `aquatone_urls.txt`, `aquatone_session.json`, a log file, and `headers/`, `html/` and `screenshots/` directories under the given output directory — open the HTML report in a browser to review the attack surface at a glance.

### Read hosts from a file with a wider port set

Skip the pipe and expand the default 80,443,8080,8443 scan to a built-in larger list.

```
aquatone -input-file targets.txt -ports large
```

Outcome: probes each host on the `large` built-in port list (80, 81, 443, 3000, 8000, 8080 and dozens more) instead of only the four defaults, catching web apps on unusual ports.

### Consume Nmap or Masscan XML

Feed an XML scan directly instead of a text list.

```
aquatone -nmap -input-file scan.xml
```

Outcome: parses hosts and open ports straight from the Nmap/Masscan XML output, so only ports the scanner already found open are checked.

### Re-generate a report from a saved session

Rebuild the HTML report later without re-scanning.

```
aquatone -session aquatone_session.json
```

Outcome: loads the previously saved session file and writes a fresh HTML report from the stored page data — useful when the template or clustering settings should be applied again.

### Only screenshot specific status codes

Cut noise by restricting which responses get captured.

```
cat hosts.txt | aquatone -match-codes 200,302
```

Outcome: only hosts answering with the listed HTTP status codes are treated as valid web pages, so redirects-to-nothing and dead endpoints are filtered out of the report.

*No corpus evidence — derived from upstream documentation.*
