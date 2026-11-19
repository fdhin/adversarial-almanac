---
name: "witchxtool"
categories:
  - blackarch-webapp
version: "1.1"
homepage: "http://packetstormsecurity.com/files/97465/Witchxtool-Port-LFI-SQL-Scanner-And-MD5-Bruteforcing-Tool.1.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "iqbalexploit/witchxtool"
github-stars: 0
github-pushed: "2018-08-13"
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

# witchxtool

> A menu-driven Perl script bundling a port scanner, LFI scanner, dork-driven SQLi and LFI scanners, a proxy-list scanner and an MD5 bruteforcer.

## What it's for

A 2011-era "hacking toolkit" in a single Perl script (by th3_w1tch of the Makassar Ethical Hacker project) that combines seven utilities behind one interactive console menu: a port scanner, an LFI scanner, an MD5 hash bruteforcer, a dork-based SQL injection scanner, a fresh-proxy scanner, a dork-based LFI scanner and a Yahoo password checker. It is entirely menu-driven — every function prompts for its input (target host, dork, hash, wordlist choices) on stdin rather than taking command-line arguments. It predates modern web defenses and none of its scanners understand TLS-only or JS-rendered sites, so treat it as a legacy dork-scanning utility rather than a modern web application scanner; each function returns to the menu when it finishes. It needs the `WWW::Mechanize`, `MD5`, `Data::Validate::IP` and `Net::RawIP` Perl modules.

## Typical use cases

### Launch the toolkit menu

Start the interactive console and pick a function by number.

```
witchxtool
```

Outcome: prints the banner and program menu (1. Port Scanner, 2. LFI Scanner, 3. MD5 Brute Force, 4. Dork SQLI Scanner, 5. Proxy Fresh Scanner, 6. Dork LFI Scanner, 7. Yahoo Password Checker, 8. Help) and waits on `Insert Program Number`; entering a number starts that function, which then prompts for its own inputs — e.g. the port scanner asks for a target host such as `192.168.1.1`, the LFI scanner for a URL ending in a parameter like `http://example.com/index.php?page=`, the MD5 bruteforcer for a hash and wordlist options, and the dork scanners for a dork such as `news.php?id=`. Each function returns to the menu on Enter when done.

*No corpus evidence — derived from upstream documentation.*
