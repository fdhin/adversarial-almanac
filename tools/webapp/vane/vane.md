---
name: "vane"
categories:
  - blackarch-webapp
version: "1899.48f9ab5"
homepage: "https://github.com/delvelabs/vane"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "delvelabs/vane"
github-stars: 210
github-pushed: "2019-08-23"
github-archived: true
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vane

> GPL Ruby fork of WPScan: black-box WordPress scanner that enumerates versions, plugins, themes and users, checks them against vulnerabilities, and brute-forces passwords.

## What it's for

Vane (Delve Labs) is the free-software fork of WPScan created when WPScan went non-free — a black-box WordPress vulnerability scanner in Ruby. Pointed at a WordPress site it fingerprints the core version, enumerates installed plugins, themes, timthumb scripts and user accounts, checks findings against its vulnerability data, and can brute-force login credentials with a wordlist. The CLI is the classic WPScan argument set. The repository was archived in 2019 and its vulnerability knowledge is frozen at that era: treat its findings as historical leads and verify against current WordPress advisory sources before reporting. Ruby 1.9+ on Unix-likes; Windows is not supported.

## Typical use cases

### Run the default enumeration scan

The standard first pass.

```
vane -u http://target.example.com
```

Outcome: verifies the site runs WordPress, then enumerates the default set (vulnerable plugins and themes, timthumbs, users), printing the WP version and any known-vulnerable components it matches.

### Enumerate all plugins and themes

Full inventory instead of known-vulnerable only.

```
vane -u http://target.example.com -e ap,at
```

Outcome: enumerates ALL installed plugins and all themes (many requests, slow) rather than only those with known vulnerabilities — use when the inventory itself matters.

### Brute-force passwords

Attack the login.

```
vane -u http://target.example.com --wordlist /usr/share/wordlists/rockyou.txt
```

Outcome: after enumerating usernames, runs the password brute-forcer over each found user with the supplied wordlist and reports any valid credentials; -U user restricts the attack to a single account.

### Scan a site that hides its WordPress-ness

For installs that obscure headers or wp-content.

```
vane -u http://target.example.com --force -e p
```

Outcome: skips the "is this WordPress?" sanity check (--force) and enumerates plugins directly; --wp-content-dir and --wp-plugins-dir override detected paths for renamed installs.

No corpus evidence — derived from upstream documentation (README/VANE ARGUMENTS of delvelabs/vane).
