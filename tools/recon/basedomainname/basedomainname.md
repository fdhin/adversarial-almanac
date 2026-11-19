---
name: "basedomainname"
categories:
  - blackarch-recon
version: "0.1"
homepage: "http://www.morningstarsecurity.com/research"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/basedomainname"
github-stars: 0
github-pushed: "2019-05-12"
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

# basedomainname

> Reduce a column of FQDNs to their registered base domain, extension, TLD or host label.

## What it's for

A small Ruby filter by Andrew Horton (urbanadventurer, MorningStar Security) that reads fully qualified domain names on stdin (or from a file) and prints the part you care about: the registrable base domain (`dev12.wlg.cats.com` → `cats.com`), the extension/second-level+TLD (`www.mice.co.uk` → `co.uk`), the bare TLD (`www.wolves.mobi` → `mobi`), or the host label (`www.kangaroo.com.au` → `www`). The classic use is normalizing large subdomain-enumeration outputs down to distinct organizations before reporting. It knows the difference between a `co.uk`-style public suffix and a generic TLD via a built-in suffix table, which plain `cut`/`awk` tricks get wrong.

## Typical use cases

### Extract the registered domain from a host list

```
cat hosts.txt | basedomainname --domain | sort -u
```

Outcome: one base domain per input hostname (multi-level public suffixes handled), deduplicated by `sort -u` — the distinct organization list behind a pile of subdomains.

### Get the TLD or the domain extension

```
basedomainname --tld -i hosts.txt
basedomainname --ext -i hosts.txt
```

Outcome: reads from the file instead of stdin; `--tld` prints only the top-level domain (`www.panda.cn` → `cn`) while `--ext` prints the extension including a second-level registry suffix (`k.iwi.nz` → `iwi.nz`).

### Keep only the host label

```
cat hosts.txt | basedomainname --host
```

Outcome: prints the leftmost label of each FQDN (`nose.shark.int` → `nose`) — handy for spotting service names across a domain inventory.

*No corpus evidence — derived from upstream documentation (source header and built-in usage text).*
