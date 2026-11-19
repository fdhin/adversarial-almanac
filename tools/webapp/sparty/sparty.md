---
name: "sparty"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://sparty.secniche.org/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "0xdevalias/sparty"
github-stars: 98
github-pushed: "2013-11-12"
github-archived: true
github-issues: 9
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sparty

> SharePoint and FrontPage web-application configuration auditor in Python 2.

## What it's for

Sparty (by Aditya K Sood of SecNiche Security Labs, 2013) audits web applications built on
SharePoint and FrontPage architecture. It fingerprints the platform version from HTTP headers,
checks access permissions on standard SharePoint/FrontPage files and directories, dumps
credentials from exposed configuration files, and queries or exploits the FrontPage RPC
services (service listing, file upload, folder removal). It does not test anything beyond
SharePoint/FrontPage-specific misconfigurations, and the packaged version requires Python 2.
Always give the URL with an explicit `http://` or `https://` scheme and without a trailing slash.

## Typical use cases

### Fingerprint the platform from HTTP headers

Determine whether a target is SharePoint or FrontPage and which version it runs.

```
sparty -u http://target.example.com -v ms_sharepoint
```

Outcome: prints the detected platform/version from the server's HTTP response headers; use `ms_frontpage` instead for FrontPage targets.

### Check access permissions on standard files

Probe the well-known SharePoint/FrontPage directories for files that are readable without authentication.

```
sparty -u http://target.example.com -s forms
```

Outcome: reports which standard files under the chosen directory (`forms`, `layouts`, or `catalog` for SharePoint; `-f pvt|bin` for FrontPage `_vti`/bin paths) are accessible.

### Dump credentials from exposed configuration files

Pull entries out of default SharePoint/FrontPage configuration files left world-readable by configuration errors.

```
sparty -u http://target.example.com -d dump
```

Outcome: prints any credentials or sensitive entries recovered from exposed default files; `-d extract` is the alternative extraction mode.

### Query and exploit FrontPage RPC services

Enumerate the RPC interface of a FrontPage installation, up to file upload.

```
sparty -u http://target.example.com -e rpc_service_listing
```

Outcome: lists the RPC services the installation exposes. Other `-e` choices include `rpc_version_check`, `author_config_check`, `rpc_file_upload`, and `author_remove_folder`; exposed services indicate a vulnerable installation.

No corpus evidence — derived from upstream documentation.
