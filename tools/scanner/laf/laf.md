---
name: "laf"
categories:
  - blackarch-scanner
version: "12.7a456b3"
homepage: "https://github.com/takeshixx/laf"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "takeshixx/laf"
github-stars: 14
github-pushed: "2014-09-21"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# laf

> Login Area Finder: probes a host or host list for admin/login panels by requesting known paths.

## What it's for

laf (takeshixx, "Login Area Finder") is a small single-file Python scanner that
locates administrative login panels on web servers. It walks a built-in list of
well-known admin paths, requesting each and reporting the ones that return a valid
page rather than a 404 — the quick way to find the login surface of a web
application before feeding credentials to it. Path sets are selectable per
technology (`-sys`: generic directories, php, cfm, asp, pl, html, phpMyAdmin) and
several can be combined. It takes a single host or a list from a file or stdin,
supports HTTP basic and digest authentication plus a session cookie for hosts that
need a login first, and can ignore invalid TLS certificates. The project is a 2014
one-shot script (repository archived) — pair it with a modern content discovery
tool when path coverage matters.

## Typical use cases

### Find login panels on a single host

```
laf -d example.com
```

Outcome: Requests the default directory wordlist paths on the host and prints every path that answered with a valid page — the admin/login URLs to target next. Add `-ic` when the host serves a self-signed certificate.

### Sweep a host list with technology-specific paths

```
laf -l hosts.txt -sys php,asp
```

Outcome: Reads one host per line and probes the PHP- and ASP-specific admin path sets on each, reporting panels per host — useful when the technology mix is known and the generic list is too noisy. Add `-k` to stop scanning a host at its first valid panel.

### Feed hosts from another tool over stdin

```
cat hosts.txt | laf -l -
```

Outcome: Reads the host list from stdin (`-` in place of a filename), so the output of any subdomain-resolver or port-scanner pipeline can be piped straight in and enriched with discovered login panels.

### Probe behind authentication

```
laf -d example.com -u user -p Password1 -da
```

Outcome: Sends HTTP digest authentication credentials (`-da`; basic auth is used without it) with every request, letting the scanner enumerate admin paths on hosts that gate all content behind an auth prompt.

*No corpus evidence — derived from upstream documentation.*
