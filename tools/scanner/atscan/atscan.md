---
name: "atscan"
categories:
  - blackarch-scanner
version: "2461.06521a9"
homepage: "https://github.com/AlisamTechnology/ATSCAN-V3.1"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: MIT
meta-checked: "2026-11-09"
github-repo: "AlisamTechnology/ATSCAN-V3.1"
github-stars: 1590
github-pushed: "2024-08-10"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# atscan

> Perl search-engine and dork scanner — drives Bing, Google, Ask, Yandex and Shodan queries to collect targets, then tests them: harvests emails/IPs, detects WordPress and SQLi/LFI patterns, port-scans ranges, or runs an external command per result.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Automating the dork-to-target-to-test pipeline. You give it a dork (or a file of dorks) and a result depth; it pages through the chosen search engine(s), collects matching URLs and IPs, and either mines them directly (emails, IPs, regex extraction) or feeds them to built-in detectors (`--wp`, `--sql`, `--lfi`, admin finders) and to TCP/UDP port checks. Results can be filtered by status code, in-URL string, regex or response content, deduplicated, and saved. Every discovered host can also be pushed through an arbitrary external command (`--command`) with placeholders for the URL, host and IP, which turns it into a target generator for nmap, nikto or custom scripts. It is a collection-and-detection front end, not a fuzzer with its own vulnerability database.

## Typical use cases

### Run a dork across search engines

```
atscan --dork "index of /backup" --level 5 -m bing --save
```

Outcome: pages five levels deep through Bing results for the dork, collecting matching URLs/IPs into output files. `-m` selects the engine — `google`, a comma-separated subset, or `all`; `--level`/`-l` controls how many result pages are processed.

### Harvest emails and IPs from dork results

```
atscan -d dorks.txt -l 3 --email --ips
```

Outcome: runs every dork in the file, crawls the results, and extracts email addresses and IP addresses into the harvest output — a bulk OSINT sweep over a dork list.

### Port-scan a host range

```
atscan -t 198.51.100.1-20 --port 22,80,443 --tcp
```

Outcome: TCP connect checks across the address range and port list, reporting open ports; `--udp` switches to UDP. Works the same on targets collected by a dork.

### Detect WordPress targets

```
atscan -t example.com --wp
```

Outcome: probes the target and flags WordPress sites; pair with `--sql`, `--lfi` or the exploit options (`--exp`/`--expHost` with a payload) to move from detection to injection testing on the found sites.

### Chain an external command over every result

```
atscan --dork "inurl:admin login.php" --level 3 --command "nmap -sV -p 80,443 --HOSTIP"
```

Outcome: for each host the dork yields, the command runs once with `--HOSTIP` replaced by the found IP (`--TARGET` and `--HOST` are the URL-form placeholders), streaming per-host output — ATSCAN as a target feeder for your own tooling.

### Query Shodan instead of a search engine

```
atscan --shodan --search "apache" --apikey <SHODAN_API_KEY>
```

Outcome: pulls matching hosts from the Shodan API and applies the same collection, detection and `--command` machinery to the results.
