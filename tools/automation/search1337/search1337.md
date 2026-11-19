---
name: "search1337"
categories:
  - blackarch-automation
version: "11.bf03ec9"
homepage: "https://github.com/b3mb4m/Search1337"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "b3mb4m/Search1337"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# search1337

> Command-line search front-end for the 1337day/Inj3ct0r exploit marketplace: pass a keyword (and optionally a CVE) and it prints matching exploit titles with their 1337day URLs.

## What it's for

Querying the 1337day exploit database from the terminal during reconnaissance — `search1337 --e proftpd` style — instead of browsing the site, with an optional `--cve` filter to narrow results to a specific CVE. It is a thin Python 2 client that scrapes the 1337day marketplace and prints exploit name plus URL pairs. Two honest caveats: the tool is unmaintained (the original b3mb4m repository is gone; BlackArch now builds from a community fork, idkwim/Search1337, whose README documents the CLI) and, more fundamentally, the 1337day site itself has been effectively dead/parked for years, so live queries return nothing. Treat it as a historical relic of the exploit-marketplace era rather than a working OSINT tool — searchsploit or an ExploitDB mirror covers the same need today.

*No corpus evidence — derived from upstream documentation (community fork of the deleted original repo).*

## Typical use cases

### Search the exploit database by keyword

```
search1337 --e proftpd
```

Outcome: prints one line per matching database entry — exploit title and its 1337day URL (for example `ProFTPd 1.3.5 - Remote Command Execution Exploit` / `http://1337day.com/exploit/23544`) — paginated by the site's response. When the service was live, this was the quick "is there a public exploit for this software/version" check; against the dead site it returns no results.

### Narrow a search to a specific CVE

```
search1337 --e wordpress --cve 2017-1000000
```

Outcome: passes the CVE value through to the 1337day query so entries referencing it are the only ones returned; the upstream README documents the flag simply as "you can append CVE number" without pinning a stricter format. Running the bare command `search1337` with no arguments prints the tool's usage banner and exits — a quick way to confirm which arguments the installed build supports.
