---
name: "dmitry"
categories:
  - blackarch-scanner
version: "1.3a"
homepage: "http://www.mor-pah.net/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-02-01"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "jaygreig86/dmitry"
github-stars: 241
github-pushed: "2026-03-27"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# dmitry

> Deepmagic Information Gathering Tool: one command that chains whois, subdomain, email and portscan lookups on a host.

## What it's for

DMitry (Deepmagic Information Gathering Tool, originally coded by "kernel"
of the deepmagic crew) is a C command-line utility that gathers as much
information as possible about a single host in one run: IP/domain whois,
Netcraft data, subdomain and email searches, and a TCP portscan with banner
grabbing. Its point is removing the need to type each of those queries
separately when you need a quick profile of a target. Some modules depend
on long-dead scraping backends (Google/Netcraft parsing is known-broken in
recent years), so treat whois and the TCP portscan as the reliable core.

## Typical use cases

### Run everything with defaults

Quick one-shot profile of a host.

```
dmitry target.example.com
```

Outcome: performs the majority of functions against the target and saves
the collected output to `target.example.com.txt` in the current directory —
one file with whois, search and portscan sections.

### Pick specific modules

Run only the lookups you want.

```
dmitry -iwnp -t 7 target.example.com
```

Outcome: performs an IP whois (`-i`), domain whois (`-w`), Netcraft
information lookup (`-n`) and a TCP portscan (`-p`) with a 7-second
per-port TTL (`-t 0-9`, default 2); modules combine freely in one flag
group and results print to stdout.

### Subdomain and email search with a report file

Collect naming intel before further enumeration.

```
dmitry -s -e -o dmitry-target.txt target.example.com
```

Outcome: searches for possible subdomains (`-s`) and email addresses
(`-e`) associated with the domain and writes the whole session to
`dmitry-target.txt` instead of only stdout.

### Portscan with banners and filtered ports

Map open services on a host.

```
dmitry -pfb 198.51.100.10
```

Outcome: TCP portscan (`-p`) that also reports filtered ports (`-f`) and
grabs banners from open ports (`-b`); banners frequently reveal the exact
server software for follow-up enumeration.

*No corpus evidence — derived from upstream documentation (jaygreig86/dmitry,
the maintained repo of the original Deepmagic tool; flags verified against
its README/man page).*
