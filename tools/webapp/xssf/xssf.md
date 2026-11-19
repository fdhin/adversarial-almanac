---
name: "xssf"
categories:
  - webapp
version: "3.0"
homepage: "https://code.google.com/p/xssf/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "sempervictus/xssf"
github-stars: 18
github-pushed: "2012-08-23"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xssf

> A Cross-Site Scripting Framework that runs as a Metasploit plugin and turns an XSS hole into a controlled attack channel on the victim's browser.

## What it's for

The Cross-Site Scripting Framework (XSSF, by Ludovic Courgnaud / CONIX Security) creates a communication channel with a targeted browser through an XSS vulnerability, so further attacks can be run against that browser from `msfconsole`: payload modules selected per browser, launching Metasploit browser exploits directly from the XSS, tunneling web traffic through the victim's session (XSSF Tunnel) for intranet-only applications, and logging everything to a web GUI. It is an exploitation framework only — it deliberately does not find XSS vulnerabilities, and it requires a Metasploit installation with a working database. The original Google Code project is gone; the packaged tree (mirrored at sempervictus/xssf) is loaded into Metasploit as a plugin.

## Typical use cases

### Load the plugin with custom listener options

```
load xssf Port=80 Uri=/xssf/ Public=true Mode=Verbose
```

Outcome: from `msfconsole`, XSSF starts its victims server on port 80 under `/xssf/`, its attacker server (GUI + Tunnel) on the next port (81), exposes both remotely (`Public=true`) and prints all attack/tunnel messages; plain `load xssf` uses the defaults.

### Track connected victims

```
xssf_victims
```

Outcome: lists every browser that executed the XSSF hook, with IDs used by all other commands; `xssf_active_victims` filters to currently connected ones and `xssf_information 8` shows full details for victim 8.

### Tunnel through a victim's session

```
xssf_tunnel 8
```

Outcome: opens the XSS tunnel so pages on the affected domain can be browsed from the attacker's machine using victim 8's session — useful when cookie theft alone is not enough (intranet portals).

### Fire a Metasploit browser exploit at a victim

```
xssf_exploit 0 8,9-12
```

Outcome: launches MSF browser-exploit job 0 (see `jobs`) against the selected victims through the XSS channel, turning the client-side XSS into a potential system compromise.

### List the URLs XSSF serves

```
xssf_urls
```

Outcome: prints the hook, test-page and resource URLs to embed in the vulnerable page so browsers connect back to the framework.

*No corpus evidence — derived from upstream documentation (the packaged `README_XSSF` and `data/xssf/gui/help.html` user guide).*
