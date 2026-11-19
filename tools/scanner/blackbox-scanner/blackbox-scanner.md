---
name: "blackbox-scanner"
categories:
  - blackarch-scanner
version: "1.7a25220"
homepage: "https://github.com/sepehrdaddev/blackbox"
packaged-by: [blackarch]
blackarch-builddate: "2018-12-21"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sepehrdaddev/blackbox"
github-stars: 31
github-pushed: "2018-12-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blackbox-scanner

> Python 2 attack console: Google/Bing dork scanning, web vulnerability scanning, hash cracking, and brute forcing in one modular CLI.

## What it's for

BLACKBOx (v2.2, by Saad Hat / sepehrdaddev) is a modular framework where every capability is a subcommand: `google_dorker` and `bing_dorker` harvest URLs via dorks and test them for LFI/SQLi/XSS/RCE, `scan_list` does the same for a prepared URL list, `hash_killer` cracks MD5–SHA512 and database hashes against a wordlist, `admin_brute` locates admin panels, and bruteforcers cover WordPress/SSH/FTP. A shelf of 2015-era exploit modules (Joomla, Magento, phpMoAdmin, ElasticSearch, LotusCMS, PrestaShop) rounds it out. It is Python 2 only and dormant since 2018 — the exploit targets have been patched for years, so treat it as a legacy toolkit whose dorking, scanning, and cracking modules remain usable against lab targets.

## Typical use cases

### Dork-scan search engine results for SQLi

```
blackbox-scanner google_dorker --dork="php?id=" --level 10 --sqli
```

Outcome: pages through 10 levels of Google results for the dork, then tests every URL found for SQL injection; swap `--sqli` for `--lfi`, `--xss`, or `--rce`, or use `bing_dorker` for the Bing variant.

### Scan a prepared URL list

```
blackbox-scanner scan_list -l urls.txt --sqli
```

Outcome: reads one URL per line from urls.txt and reports which ones are vulnerable to the selected class (LFI/SQLi/RCE/XSS).

### Crack a hash dump

```
blackbox-scanner hash_killer -w wordlist.txt --md5 hashes.txt
```

Outcome: matches each MD5 line of hashes.txt against wordlist.txt; `--sha1`, `--sha256`, `--mysql41`, `--oracle11` and siblings select the other supported algorithms.

### Locate the admin panel

```
blackbox-scanner admin_brute http://target.example.com --php
```

Outcome: probes the target for common admin paths filtered to PHP; `--asp`, `--cfm` and the other stack filters narrow it for other platforms.

### Enumerate domains on a shared IP

```
blackbox-scanner dns_info --ip 198.51.100.10 --yougetsignal
```

Outcome: reverse-lookups all websites hosted on that IP via the selected service — pass exactly one of `--yougetsignal`, `--viewdns`, or `--hackertarget`.

*No corpus evidence — derived from upstream documentation and the packaged source.*
