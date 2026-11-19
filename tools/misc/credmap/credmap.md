---
name: "credmap"
categories:
  - blackarch-misc
version: "116.d862247"
homepage: "https://github.com/lightos/credmap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lightos/credmap"
github-stars: 455
github-pushed: "2017-12-01"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# credmap

> Test one set of credentials against a catalog of popular websites to expose password reuse.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Credmap — the Credential Mapper, by Roberto Salgado (lightos) — exists to
demonstrate the danger of credential reuse: give it a username or email and a
password and it tries that pair against a built-in catalog of well-known
websites, reporting where the same credentials authenticate. Scope the run with
--only/--exclude and --safe-urls (HTTPS targets only), route checks through a
proxy, or feed bulk USER:PASSWORD files via --load. New site checks are added as
XML definitions in the websites/ folder. It audits reuse of credentials you
already have; it never guesses or cracks passwords.

## Typical use cases

### Test one account across the catalog

Check where a known credential pair is also valid.

```
credmap -u johndoe -p Password1
```

Outcome: walks every supported website, printing per-site whether the login
succeeded, and summarizes which sites accepted the pair. Add -v/-vv for detail.

### Restrict testing to specific sites

Limit noise and blast radius.

```
credmap -u johndoe -p Password1 --only "linkedin.com, facebook.com"
```

Outcome: tests only the named sites (--exclude is the inverse); --safe-urls
restricts to HTTPS targets so credentials are never sent in cleartext.

### Audit a dumped credential list

Sweep a USER:PASSWORD file across the catalog.

```
credmap --load creds.txt --format "u:p"
```

Outcome: parses each line per the --format string (fields u, e and p with custom
separators, e.g. `"u|e:p"`) and reports every site where a line's credentials
work — the account's credential-reuse exposure map.

### Route checks through a proxy

Observe the traffic or hide its origin.

```
credmap -u johndoe -p Password1 --proxy "socks5://198.51.100.10:9050"
```

Outcome: all website checks go through the given HTTP or SOCKS proxy;
-i/--ignore-proxy instead bypasses the system's default proxy.

### List supported websites

See the current catalog before scoping a run.

```
credmap --list
```

Outcome: prints every website credmap can test, matching the XML definitions in
its websites/ directory.
