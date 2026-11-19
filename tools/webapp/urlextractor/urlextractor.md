---
name: "urlextractor"
categories:
  - blackarch-webapp
version: "19.739864d"
homepage: "https://github.com/eschultze/URLextractor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "eschultze/URLextractor"
github-stars: 459
github-pushed: "2019-05-27"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# urlextractor

> Bash recon pipeline that dumps hosting, DNS, ASN, whois, robots.txt, directory-fuzz and third-party-API findings for a website in one run.

## What it's for

URLextractor (eschultze) is an information-gathering and website-reconnaissance script: hand it a URL and it collates a wide picture of the site — IP and hosting info (city, country), DNS servers, ASN/network range/ISP, load-balancer tests, whois abuse contacts, PAC file, robots.txt entries (recursively mined for hidden paths), source-code secrets (passwords, users), external frames, and a directory fuzz using the DirBuster wordlist — plus API lookups (URLVoid, VirusTotal, Shodan, Alexa/Google ranks) and a set of correlation links. It is an aggregator: each check shells out to standard utilities (dig, whois, curl, lynx) and public APIs, so results are only as current as those endpoints. Several 2016-era API integrations require keys in the configuration or have changed since; a first-run check installs missing dependencies.

## Typical use cases

### Run the full recon dump for a site

One command, everything it knows.

```
urlextractor http://target.example.com/
```

Outcome: prints a sectioned report — IP/hosting info, DNS, ASN/network, load-balancer check, whois abuse mail, robots.txt paths, source-code password hints, external links, directory-fuzz hits and API verdicts — and keeps a log file for the hostname in the working directory.

### Colorize the report for triage

Make the interesting lines stand out.

```
urlextractor http://target.example.com/ | colorex -g "INFO" -r "ALERT"
```

Outcome: pipes the report through colorex so INFO lines render green and ALERT lines red, making quick skimming of the dump easier (requires the colorex Python package).

No corpus evidence — derived from upstream documentation (README of eschultze/URLextractor).
