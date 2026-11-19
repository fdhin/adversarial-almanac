---
name: "wpseku"
categories:
  - blackarch-webapp
version: "39.862fb2c"
homepage: "https://github.com/m4ll0k/WPSeku"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-04"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m4ll0k/WPSeku"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# wpseku

> Black-box WordPress security scanner: remote checks, login brute force, and local source audit.

## What it's for

WPSeku (by Momo Outaadi, m4ll0k) is a black-box WordPress vulnerability scanner in Python 3. Against a remote target it fingerprints server software, flags uncommon headers such as `X-Pingback`, checks full-path disclosure, `wp-config` backup exposure, common files (`robots.txt`, `xmlrpc.php`, `readme.html`), and directory listings. It can also brute-force logins via XML-RPC, and statically audit local WordPress plugin/theme/core source code for dangerous patterns. The upstream repository has been deleted; the usage below is from the last archived upstream README (v0.4.0), which matches the packaged version.

## Typical use cases

### Run a generic scan

```
wpseku --url https://target.example.com --verbose
```

Outcome: prints the banner and per-check findings — server software, uncommon headers, full-path disclosure, `wp-config` backup availability, common files found, and any directory listings enabled.

### Brute-force the login via XML-RPC

```
wpseku --url https://target.example.com --brute --user admin --wordlist passwords.txt --verbose
```

Outcome: attempts the given user against the wordlist through XML-RPC; valid credentials are printed in a username/password table.

### Audit local plugin or theme code

```
wpseku --scan ./wp-content/plugins --verbose
```

Outcome: statically scans the PHP files under the path for risky patterns, printing a line-numbered table of possible vulnerabilities (e.g. cross-site scripting via unfiltered `$_GET`/`$_POST` usage).

*No corpus evidence — derived from upstream documentation (README recovered from the web archive after the repository was deleted).*
