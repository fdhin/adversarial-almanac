---
name: "pureblood"
categories:
  - blackarch-automation
version: "37.2c5ce07"
homepage: "https://github.com/cr4shcod3/pureblood"
packaged-by: [blackarch]
blackarch-builddate: "2019-03-22"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "cr4shcod3/pureblood"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# pureblood

> "Pure Blood": a menu-driven web-pentest toolkit (v2) bundling information gathering, WordPress attacks and automated union-based SQL injection behind a numbered Python menu.

## What it's for

Console-accessible web recon and attack chores. Its Information Gathering menu covers banner grabbing, whois, traceroute, DNS records, reverse DNS, zone-transfer checks, port scanning, admin-panel discovery, subdomain scanning, CMS identification, reverse-IP and subnet lookups, link extraction, directory and file fuzzing, and Shodan search/host lookup. The Web Application Attack side wraps WPScan (including bruteforce) plus a WordPress plugin vulnerability checker, and its headline feature is Auto SQL Injection — automated union-based SQLi with detection that works even without error output. A Generator section makes deface pages, passwords and text-to-hash conversions. It orchestrates known libraries (requests, dnspython, BeautifulSoup, Shodan, whois) rather than shipping novel techniques, and expects some wrapped tools (WPScan) to be on PATH. Historical status: the author's repository is gone (deleted around 2019–2021; documentation survives via the Internet Archive's 2018 capture of the repo, and the packaged Python 3 build is what BlackArch still ships). Use it as a quick menu of basics; each function has stronger dedicated modern tools.

*No corpus evidence — derived from upstream documentation (2018 archived snapshot of the deleted repository).*

## Typical use cases

### Open the toolkit menu

```
pureblood
```

Outcome: displays the numbered main menu (Web Pentest / Information Gathering, Web Application Attack, Generator). Pick a category, then a function, and answer the prompts for the target domain or host — e.g. choosing Subdomain Scan under Information Gathering asks for the domain and enumerates subdomains; choosing Admin Panel Scan hunts for common admin paths on the target site. Shodan entries prompt for your Shodan API key.

### Run an automated SQL injection check

From the menu, choose Web Application Attack and Auto SQL Injection:

```
pureblood
```

Outcome: after prompting for the target URL/parameter, it attempts automated union-based detection (explicitly designed to work when the page shows no database errors) and reports exploitable injection points — the module the author built the tool around. The WordPress branch works the same way: WPScan and its bruteforce wrap the external wpscan binary, and the plugin checker matches the target's plugins against a list of known-vulnerable ones.
