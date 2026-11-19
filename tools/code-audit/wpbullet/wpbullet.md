---
name: "wpbullet"
categories:
  - blackarch-code-audit
version: "34.6185112"
homepage: "https://github.com/webarx-security/wpbullet"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "webarx-security/wpbullet"
github-stars: 240
github-pushed: "2022-09-12"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpbullet

> Static analysis for WordPress plugins and themes (and plain PHP) — pattern-based modules flag SQL injection, XSS, file-upload and related sinks across a code tree, from a local path or a wordpress.org URL.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Triaging PHP code for the vulnerability classes that dominate WordPress exploits before you read a line: it walks a plugin/theme tree and applies per-vulnerability detection modules that pair dangerous function calls (`$wpdb->query`, `echo`, `move_uploaded_file`, ...) with user-input sources, skipping inputs guarded by known sanitizers on the module's blacklist. Reach for it when auditing an unfamiliar plugin or theme, or vetting a third-party one before deployment. It is pattern-driven rather than a full PHP interpreter — findings are leads with file/line context, not proofs, so confirm each hit by reading the surrounding code. The `--path` can equally be a wordpress.org plugin URL, and modules can be enabled/disabled individually or extended with your own module classes.

## Typical use cases

### Audit a locally installed plugin

```
wpbullet --path=/var/www/wp-content/plugins/example-plugin
```

Outcome: every PHP file under the path is run through the enabled modules; findings print to the console with the vulnerability class, file and line, ordered by module. (The packaged `wpbullet` wrapper runs `wpbullet.py` from its install directory.)

### Scan a plugin straight from wordpress.org

```
wpbullet --path="https://wordpress.org/plugins/example-plugin"
```

Outcome: the current version of the plugin is downloaded and unpacked into a `.temp` folder automatically, then scanned like a local copy — no manual fetch step; add `--cleanup` to remove the `.temp` content when the scan finishes. A direct zip URL (`https://downloads.wordpress.org/plugin/...zip`) works the same way and pins the exact version.

### Run only chosen vulnerability modules

```
wpbullet --path=. --enabled="SQLInjection,CrossSiteScripting"
```

Outcome: only the named modules execute, making a fast targeted pass (for example SQL-injection-only); `--disabled` is the inverse — run everything except the listed modules.

### Save findings as a JSON report

```
wpbullet --path=/srv/audit/example-theme --report
```

Outcome: besides the console output, the findings are saved in JSON format under the `reports/` directory, giving you a file to diff between plugin versions or attach to an audit trail.
