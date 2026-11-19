---
name: "adfind"
categories:
  - blackarch-webapp
version: "v1.0.3.r3.g46acf19"
homepage: "https://github.com/sahakkhotsanyan/adfind"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-04"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sahakkhotsanyan/adfind"
github-stars: 41
github-pushed: "2025-09-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# adfind

> Go-based admin-panel finder that probes a site for common administrative paths, per platform family.

## What it's for

adfind brute-forces well-known admin-panel paths (admin/, administrator/, wp-login.php, panel-administracion/, ...) against a web site for a chosen platform family (php, asp, aspx, js, cfm, cgi, brf) and reports which ones answer. Use it during recon to locate management interfaces the main site does not link. It only discovers paths by response — it does not test credentials or exploit anything. The bundled path lists and config ship under `/usr/share/adfind/` and can be swapped for your own.

## Typical use cases

### Scan a site for admin panels

```
adfind -u http://target.example.com -v
```

Outcome: probes the built-in candidate paths for every platform family and prints each URL that responds; `-v` logs every request as it goes.

### Limit to one platform and stop at the first hit

```
adfind -u http://target.example.com -t php -s
```

Outcome: only PHP-family paths are tested (`-t` accepts php, asp, aspx, js, cfm, cgi, brf, or all) and the scan halts on the first admin panel found.

### Tune timeouts and use a custom config base

```
adfind -u http://target.example.com -to 2000 -b /usr/share/adfind/
```

Outcome: per-request timeout is raised to 2000 ms (default 1000) and config/wordlists are loaded from the given base directory — point `-b` at your own directory to use custom path lists.

No corpus evidence — derived from upstream documentation (upstream README usage output; packaged wordlists at /usr/share/adfind/).
