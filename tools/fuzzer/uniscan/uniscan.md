---
name: "uniscan"
categories:
  - blackarch-fuzzer
version: "6.3"
homepage: "http://sourceforge.net/projects/uniscan/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "poerschke/Uniscan"
github-stars: 141
github-pushed: "2021-09-03"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uniscan

> Perl web vulnerability scanner combining crawling, directory/file checks, LFI/RFI/RCE testing and fingerprinting.

## What it's for

Uniscan (Douglas Poerschke Rocha, v6.3) crawls a target site and runs plugin-driven checks over the results: directory and file brute force, robots.txt and sitemap parsing, dynamic analysis (LFI/RFI/RCE payloads against crawled parameters and forms), static file analysis, and stress tests against discovered scripts, plus web/CMS fingerprinting, server fingerprinting and Bing/Google dork enumeration. Threads, timeouts and proxy settings live in `uniscan.conf`. The project's own README declares it dead, and its plugin databases date from the early 2010s — treat findings as leads and confirm them manually with maintained tooling.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run a full scan of a single target

Combine directory, file, robots.txt, dynamic and static checks in one pass.

```
perl uniscan.pl -u http://target.example.com/ -qweds
```

Outcome: crawls the site, prints discovered directories, files and forms, flags LFI/RFI/RCE-suspect parameters, and writes an HTML report alongside the console log.

### Stress-test discovered scripts

Apply the vulnerability plugin battery against a target with recursive checks.

```
perl uniscan.pl -u https://target.example.com/ -r
```

Outcome: runs the stress plugins over the crawled file list and reports which parameters reflect or execute the injected payloads.

### Scan a list of sites in the background

Queue many URLs from a file.

```
perl uniscan.pl -f sites.txt -bqweds
```

Outcome: every URL in `sites.txt` is scanned in turn as a background job, producing one report per site in the report directory.

### Fingerprint the application and server

Identify the CMS and the server software before deeper testing.

```
perl uniscan.pl -u http://target.example.com/ -gj
```

Outcome: `-g` matches the site against Uniscan's CMS/plugin databases and grabs banners, `-j` fingerprints the server software; both results appear in the console output and the HTML report.

### Enumerate attack surface via search engines

Discover hosts and pages related to a target through Bing.

```
perl uniscan.pl -i "ip:198.51.100.10"
```

Outcome: queries Bing for pages referencing the given IP and scans every URL found; `-o "inurl:query"` is the Google-dork equivalent.
