---
name: "wordpot"
categories:
  - blackarch-honeypot
version: "44.e96889b"
homepage: "https://github.com/gbrindisi/wordpot"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "gbrindisi/wordpot"
github-stars: 188
github-pushed: "2023-02-07"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordpot

> WordPress honeypot that poses as a fully configurable blog to log probes for vulnerable plugins, themes, timthumb and other fingerprinting URLs.

## What it's for

Wordpot (Gianluca Brindisi) emulates a WordPress site convincingly enough to catch the reconnaissance wave that hits any WordPress-looking host: enumeration of installed plugins and themes, requests for timthumb.php and other common files, and the URL patterns scanners use to fingerprint a WordPress installation before exploiting it. The decoy is configurable through `wordpot.conf` or command-line flags — blog title, announced WordPress version, served theme, fake plugin and theme lists — so the surface can be tailored to what a real estate runs (or to what attackers should believe runs there). It is a small Python/Flask application extensible with plugins that mimic specific vulnerable components, and it serves fake content only: it never executes real PHP, so exploits requiring genuine WordPress behaviour are out of scope. It is a sensor for probing traffic, not an IDS.

## Typical use cases

### Serve a tailored fake blog

Present a WordPress decoy with chosen identity parameters.

```
wordpot --host 0.0.0.0 --port 80 --title "Company Blog" --ver 4.0
```

Outcome: the honeypot serves a WordPress-looking blog on port 80 announcing version 4.0 with the given title; requests for plugin, theme and timthumb paths receive plausible fake responses and are logged for review.

### Run purely from the config file

Keep the decoy's identity in `wordpot.conf` instead of flags.

```
wordpot
```

Outcome: settings are read from wordpot.conf in the application directory (host, port, title, theme, fake plugins/themes, version), so the same command serves different personas on different hosts by swapping the config file.

*No corpus evidence — derived from upstream documentation.*
