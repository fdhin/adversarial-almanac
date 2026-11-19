---
name: "davscan"
categories:
  - blackarch-webapp
version: "30.701f967"
homepage: "https://github.com/Graph-X/davscan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Graph-X/davscan"
github-stars: 120
github-pushed: "2019-07-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# davscan

> WebDAV scanner that fingerprints the server, walks PROPFIND listings, and maps findings to public exploits.

## What it's for

DAVScan targets WebDAV-enabled webservers: it fingerprints the server from response headers, issues a root PROPFIND and spiders the XML listing to discover hidden files and folders, then searches for public exploits matching the fingerprint (DoS and Metasploit-module suggestions can be excluded). It includes an IIS 6.0 Unicode auth-bypass check and supports basic auth and proxying — proxying is the workaround for authentication schemes its Python 2 requests stack cannot speak. For mounting DAV shares interactively see [[cadaver]].

## Typical use cases

### Fingerprint and walk a DAV root

```
davscan https://target.example.com
```

Outcome: fingerprints the server, spiders everything visible from a root PROPFIND (recursing by default), prints findings with the matching exploit suggestions, and writes the output to /tmp/davout.

### Authenticated scan with bounded depth

```
davscan -D 2 -u user -p 'Password1' -a basic https://target.example.com
```

Outcome: authenticates with basic auth and limits the spider to two folder levels instead of unlimited recursion.

### Scan through a proxy without exploit noise

```
davscan -P http://user:pass@198.51.100.10:8080/ -d -m -o davout.txt https://target.example.com
```

Outcome: routes all requests through the given proxy and excludes DoS (`-d`) and Metasploit-module (`-m`) results from the exploit search, writing output to davout.txt.

_No corpus evidence — derived from upstream documentation (usage block in the project README)._
