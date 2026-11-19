---
name: "cpfinder"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/118851/Control-Panel-Finder-Script.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cpfinder

> Interactive Perl script that brute-forces a built-in list of ~250 admin-panel paths against a website and flags pages whose content looks like a login form.

*No corpus evidence — derived from upstream documentation (the packaged Cp-Finder.pl by AmirMasoud, distributed via Packet Storm and read directly; the script is interactive and takes no command-line arguments).*

## What it's for

cpfinder (Cp-Finder.pl, by AmirMasoud) finds administrative web interfaces the lazy way: it asks for a target URL on stdin, then requests each path in a hardcoded list (`admin/`, `administrator/`, `wp-login.php`, `adm/`, `panel-administracion/`, `siteadmin/login.php`, …) and reports a hit when the response body contains login-form markers — Username/Password in several casings plus multilingual variants (senha, clave, usager, Wachtwoord, P/W, Admin Password, …). There are no options at all: launch it and type a site. Because detection is keyword matching on page text, any page that merely mentions "password" counts as found — visit positives by hand. Results go to stdout only; the path list is fixed, so anything not in it (non-standard panel names) stays invisible.

## Typical use cases

### Find a site's admin panel

```
cpfinder
```

At the prompt, enter a target like `target.example.com` or `target.example.com/path`.

Outcome: the script walks its built-in path list under the target (auto-prefixing `http://` and a trailing slash), printing `[-] Not Found` for misses and `[+] Ow ! Found => <url>` for each path whose content matches the login-form keywords. The flagged URLs are the admin-panel candidates to open manually.
