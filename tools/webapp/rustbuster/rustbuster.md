---
name: "rustbuster"
categories:
  - blackarch-webapp
version: "302.4a243d4"
homepage: "https://github.com/phra/rustbuster"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-10-31"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "phra/rustbuster"
github-stars: 560
github-pushed: "2023-07-21"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rustbuster

> Fast multi-protocol web fuzzer: directory/file, DNS, vhost, custom-request and IIS shortname enumeration.

## What it's for

rustbuster (by phra & ps1dr3x) is a DirBuster-style content-discovery tool
written in Rust, organized as five subcommands: `dir` for directories and
files, `dns` for A/AAAA record brute forcing, `vhost` for virtual host
enumeration, `fuzz` for arbitrary request templating with placeholder
replacement (including fetching a fresh CSRF token between requests), and
`tilde` for IIS 8.3 shortname enumeration. It handles multiple wordlists,
status-code filtering, HTTP method/body/header control and threaded
requests. Development stopped in 2023, but the feature set is stable and it
remains a quick single-binary alternative to gobuster/ffuf when one tool
should cover web, DNS and vhost discovery.

## Typical use cases

### Enumerate directories and files on a web root

Classic content discovery with a wordlist and extension list.

```
rustbuster dir --url http://target.example.com/admin-dir/ --wordlist /usr/share/seclists/Discovery/Web-Content/big.txt -e php
```

Outcome: wordlist-based brute force of the directory, printing every path
whose status code is not ignored (default ignores 404); discovered files are
the follow-up targets for manual review. This is the tool's core pattern and
seclists `big.txt` is a productive first wordlist.

### Brute-force DNS records

Resolve candidate hostnames against a domain.

```
rustbuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

Outcome: attempts an A/AAAA lookup for each wordlist entry and reports
existing subdomains — the input list for further vhost or service scanning.

### Fuzz a login endpoint with CSRF token handling

Templated POST fuzzing with two wordlists and automatic token refresh.

```
rustbuster fuzz -u http://target.example.com/login -X POST -H "Content-Type: application/json" -b '{"user":"FUZZ","password":"FUZZ","csrf":"CSRFCSRF"}' -w users.txt -w /usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-10000.txt -s 200 --csrf-url "http://target.example.com/csrf" --csrf-regex '{"csrf":"(\w+)"}'
```

Outcome: fills each `FUZZ` placeholder from the wordlists (positional
matching), refreshes the CSRF token from `--csrf-url` using the capture
regex, and reports only responses with status 200 — successful logins
surfacing as matches.

### Enumerate IIS 8.3 shortnames

Recover short filenames on IIS servers via the tilde bug.

```
rustbuster tilde -u http://target.example.com/ -e aspx -X OPTIONS
```

Outcome: enumerates tilde-shortened filenames character by character,
revealing names of files that directory listing hides; use `-X OPTIONS` when
the IIS shortname disclosure only answers OPTIONS requests.

*Corpus-grounded: `rustbuster dir --url ... --wordlist` (seclists big.txt) is the evidenced pattern; dns/fuzz/tilde/vhost verified against the upstream README usage blocks.*
