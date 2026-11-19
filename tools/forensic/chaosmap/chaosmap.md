---
name: "chaosmap"
categories:
  - blackarch-forensic
version: "1.3"
homepage: "http://freecode.com/projects/chaosmap"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-23"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "balle/chaosmap"
github-stars: 15
github-pushed: "2011-07-28"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# chaosmap

> Python 2 information-gathering scanner by Bastian Ballmann — dictionary DNS lookups, reverse-DNS/whois sweeps over IP ranges, hidden-path scans of web servers, and Google-based dork and email discovery.

## What it's for

chaosmap ("Coded by Bastian Ballmann", datenterrorist.de) combines several recon techniques behind one Python CLI: resolving DNS names from a wordlist (optionally salted with numeric variants like `www2`, `www-2`, `www02`), scanning a web server or IP range for hidden paths from a dictionary (optionally URL-encoded and through a proxy), reverse-DNS and whois lookups across an IP range, and Google-assisted lookups — trying a search engine before (or instead of) touching the target, harvesting email addresses for a domain, or running a list of Google-hacking queries against your own domains. It is a 2011-era Python 2 tool; BlackArch installs it as `chaosmap` with bundled wordlists under `/usr/share/chaosmap/dictionaries`.

## Typical use cases

### Dictionary DNS lookups with salting

```
chaosmap -f dict -d target.example.com -n -s
```

Outcome: resolves every word in the dictionary file against the domain (`-n` name lookup), generating salted variants (`-s`) such as `www2`, `www-2` and `www02`; each attempt prints the name with its resolved IP address or a resolution error.

### Scan a web server for hidden paths through a proxy

```
chaosmap -f dict -d target.example.com -w -u -P 198.51.100.10:8080
```

Outcome: requests each dictionary path on the web server (`-w`) with the paths URL-encoded (`-u`), routing all requests through the proxy at 198.51.100.10:8080 (`-P`), and reports which paths exist on the server.

### Reverse DNS and whois sweep of an IP range

```
chaosmap -i 198.51.100.1-198.51.100.254 -W -n
```

Outcome: walks the address range performing reverse lookups (`-n`) and appends whois information (`-W`) for each result — a quick inventory of which addresses are live and who owns them.

### Google-first path discovery

```
chaosmap -f dict -d target.example.com -g -w
```

Outcome: for each dictionary path, first checks whether Google already has a result for it (`-g`) and only queries the target web server directly (`-w`) when Google returns nothing — minimizing direct contact with the target.

### Harvest email addresses for a domain

```
chaosmap -d target.example.com -e
```

Outcome: runs Google searches for the domain in email mode (`-e`) and prints the addresses it finds — a passive way to collect contact addresses before phishing or username enumeration.

*No corpus evidence — derived from upstream documentation.*
