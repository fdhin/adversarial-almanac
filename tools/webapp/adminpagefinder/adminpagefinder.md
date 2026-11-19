---
name: "adminpagefinder"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/112855/Admin-Page-Finder-Script.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/AdminpageFinder"
github-stars: 0
github-pushed: "2018-01-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# adminpagefinder

> Interactive Python 2 script that brute-forces a large built-in list of administrative interface paths.

## What it's for

adminpagefinder (the Admin Page Finder script by Ajith KP, packaged from its PacketStorm release) asks for a web site and a platform family, then requests each of several hundred built-in admin-path candidates (admin/, administrator/, admin_area/, wp-login.php, bb-admin/, ...) and reports interesting HTTP responses. It is a quick single-host admin-surface locator for recon. It has no command-line flags — everything happens at the interactive prompts — and it performs no further testing of the interfaces it finds.

GitHub: third-party mirror of the upstream PacketStorm Admin Page Finder script.

## Typical use cases

### Find admin pages on a site

```
adminpagefinder
# prompts:
#   Web Site for Scan?: target.example.com
#   site source code: 1 PHP / 2 ASP / 3 CFM / 4 JS / 5 CGI / 6 BRF
```

Outcome: after confirming the host is reachable, the tool walks its built-in path list for the chosen family, printing `>>> /path Admin page found!` for HTTP 200 responses plus 302 redirects and other notable statuses, and ends with a count of admin pages found versus total pages scanned.

No corpus evidence — derived from the packaged upstream script (PacketStorm AdminpageFinder.py) and its own prompt flow.
