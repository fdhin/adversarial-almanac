---
name: "jooforce"
categories:
  - blackarch-webapp
version: "11.43c21ad"
homepage: "https://github.com/rastating/jooforce"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rastating/jooforce"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# jooforce

> Python dictionary attack against the Joomla administrator login, with user-agent spoofing and proxy rotation.

## What it's for

Jooforce (by rastating, same author as joomlavs) brute-forces the Joomla administrator login: give it the URL of an administrator page, a username, and a password file, and it walks the dictionary, spoofing user-agents and optionally rotating through a proxy list so temporary IP bans do not kill the run. When a password matches it prints it. The upstream repository has been deleted (the vault keeps the last packaged source), and the code is Python 2 era — on anything but an old Joomla instance, a maintained alternative (for example a Metasploit `joomla_bruteforce_login` auxiliary or hydra against the login form) is usually the better choice. Sibling Joomla tools in this vault: joomscan (full vulnerability scanner), joomlavs (component/module/template scanner), joomlascan (legacy RFI path checker), jomplug (component fingerprinter).

No corpus evidence — derived from upstream documentation (recovered README of the deleted repository).

## Typical use cases

### Dictionary-attack the Joomla admin login

Point it at an administrator page with a username and wordlist.

```
jooforce --url https://target.example.com/administrator --user admin --dic passwords.txt -v
```

Outcome: each password from the file (one per line) is tried against the login form, with verbose progress lines per attempt; on success the run stops with a `Login found!` message naming the working password.

### Rotate proxies to survive IP bans

Keep attacking while banned addresses get cycled out.

```
jooforce --url https://target.example.com/administrator --user admin --dic passwords.txt --proxies proxies.txt
```

Outcome: requests are routed through the proxies listed in the file (one `host:port` per line, port 80 assumed when omitted); when posting starts failing — typically a temporary ban — jooforce announces the switch to the next proxy, and if the list is exhausted it asks whether to revert to direct connection or start the rotation over.
