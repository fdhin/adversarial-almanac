---
name: "rookie"
categories:
  - blackarch-webapp
version: "v0.5.6.r2.g60ba7ae"
homepage: "https://github.com/thewh1teagle/rookie"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "thewh1teagle/rookie"
github-stars: 363
github-pushed: "2026-01-22"
github-archived: true
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rookie

> Rust library and CLI that decrypt and export cookies from local browsers on Windows, Linux and macOS.

## What it's for

rookie reads the cookie stores of locally installed browsers — Chrome,
Chromium, Edge, Brave, Firefox, Opera, Vivaldi, LibreWolf, Safari, Zen and
more — decrypts them (including Chrome's appbound encryption, which needs
admin rights on Windows from v130) and prints them as JSON or Netscape
cookie-file format. In a testing context that means turning an authenticated
browser session into a cookie jar you can replay with
curl or a proxy tool, without hand-copying values from devtools. It is a
local credential-recovery utility, not a remote attack tool: it only reads
databases of browsers on the host you run it on. The same engine is exposed
as the `rookiepy` Python package and `@rookie-rs/api` for JavaScript; the
upstream repository is archived, so the crates are the maintained surface.

## Typical use cases

### Export a browser's cookies as a Netscape cookie jar

Read one browser and filter to the domains you care about.

```
rookie --browser firefox --domains example.com --format netscape > cookies.txt
```

Outcome: prints decrypted example.com cookies in Netscape format; replay the
session with `curl -b cookies.txt https://example.com/`.

### Dump every cookie from every installed browser

With no browser selector, rookie loads from all browsers it can find.

```
rookie --load
```

Outcome: prints all recoverable cookies as JSON (default format), one object
per cookie with domain, name, value and attributes — useful for a quick
inventory of live sessions on a test workstation.

### Read cookies from a copied profile

Point rookie at a cookies database file directly, with its encryption key.

```
rookie --path /mnt/image/Users/user/AppData/Local/Google/Chrome/User/Default/Network/Cookies --key-path /mnt/image/Chrome-Key.txt
```

Outcome: decrypts the supplied store using the supplied key and prints its
cookies as JSON, without needing the browser installed on the analysis host.

*No corpus evidence — derived from upstream documentation (thewh1teagle/rookie README and cli/src/args.rs: `-p/--path`, `-k/--key-path`, `-d/--domains`, `-b/--browser`, `-l/--load`, `-f/--format netscape|json`).*
