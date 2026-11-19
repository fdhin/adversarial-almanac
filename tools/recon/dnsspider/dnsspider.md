---
name: "dnsspider"
categories:
  - blackarch-recon
version: "1.5"
homepage: "http://nullsecurity.net/tools/scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-23"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "noptrix/dnsspider"
github-stars: 16
github-pushed: "2026-04-23"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# dnsspider

> Multithreaded subdomain bruteforcer from nullsecurity.net: dictionary attacks or character-permutation bruteforce, with wildcard filtering and an AXFR pre-check.

## What it's for

dnsspider discovers hostnames under a domain either by testing a wordlist (attack type 0) or by generating character permutations with configurable charset, maximum length, prefix and postfix (type 1). Before scanning it can attempt a zone transfer, and it filters wildcard responses so scans stay usable on wildcarded domains. It queries arbitrary nameservers over UDP or TCP with thread-count, timeout and delay controls, and writes finds to a logfile in default, host-only or JSON format. It is a discovery scanner, not a general resolver — feed its output to another tool for record enrichment.

## Typical use cases

### Dictionary scan with the built-in wordlist

```
dnsspider -t 0 -a example.com
```

Outcome: tests the built-in hostname list against example.com with 50 threads (default) and prints every resolving subdomain.

### Scan a custom wordlist and save results

```
dnsspider -t 0 -a example.com -l wordlist.txt -x 100 -r found.txt
```

Outcome: 100 threads work through `wordlist.txt` (one hostname per line); discovered subdomains are written to `found.txt`.

### Character-permutation bruteforce

```
dnsspider -t 1 -a example.com -c 0 -m 3 -s www
```

Outcome: bruteforces alphanumeric combinations (`-c 0`) up to three characters long with prefix `www`, catching short internal hostnames that wordlists miss.

### Try zone transfer first, force through wildcards

```
dnsspider -t 0 -a example.com -z -f
```

Outcome: attempts an AXFR against the domain's servers before scanning, and if the scan proceeds anyway, filters wildcard responses so only real hosts are reported.

### Query specific resolvers over TCP with JSON output

```
dnsspider -t 0 -a example.com -d 198.51.100.10 -u tcp -F json
```

Outcome: sends queries to the given nameserver (comma-separate several for round-robin rotation) over TCP and emits found subdomains as JSON records.

*No corpus evidence — derived from upstream documentation.*
