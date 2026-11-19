---
name: "novahot"
categories:
  - blackarch-webapp
version: "23.69857bb"
homepage: "https://github.com/chrisallenlane/novahot"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-24"
build-system: node
license: BSD
meta-checked: "2026-11-09"
github-repo: "chrisallenlane/novahot"
github-stars: 298
github-pushed: "2025-08-10"
github-archived: true
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# novahot

> Webshell framework that pairs a JSON client API with trojans written in PHP, Python, and Ruby.

## What it's for

novahot manages the lifecycle of a webshell engagement: it generates trojan source files in several languages, each speaking a JSON-based API, then drives them from a local console. Beyond plain command execution it emulates interactive `mysql`, `psql`, and `sqlite3` clients inside the shell and implements "virtual commands" that upload, download, view, and edit remote files using your local applications. The upstream repository is archived, so the tool is frozen at its last released state.

## Typical use cases

### Seed the configuration file

```
novahot config > ~/.novahotrc
```

Outcome: writes the default config to `~/.novahotrc`; targets (a URI plus the trojan's control password) are then defined in its `targets` property.

### Generate a trojan for the target's language

```
novahot trojan view basic.php > trojan.php
```

Outcome: prints the PHP trojan source to `trojan.php`, ready for its control password to be changed and the file uploaded to a web-accessible location; run `novahot trojan list` first to see every available trojan.

### Open a shell on a configured target

```
novahot shell dvwa
```

Outcome: connects to the target named `dvwa` in `~/.novahotrc` and drops into the novahot console, where system commands and the virtual commands (`download`, `upload`, `view`, `edit`) are available.

### Pivot into an emulated database client

```
.mysql { "username" : "user", "password" : "Password1", "database" : "the-database" }
```

Outcome: switches the shell into `mysql` mode (`psql` and `sqlite3` also work), after which SQL typed at the prompt executes on the remote database through the trojan.

No corpus evidence — derived from upstream documentation.
