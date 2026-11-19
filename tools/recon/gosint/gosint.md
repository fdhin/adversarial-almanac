---
name: "gosint"
categories:
  - blackarch-recon
version: "196.9c86ed2"
homepage: "https://github.com/Nhoya/gOSINT"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Nhoya/gOSINT"
github-stars: 674
github-pushed: "2021-02-23"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gosint

> Modular OSINT Swiss-army knife: emails, breaches, PGP keys, Telegram history and Shodan from one binary.

## What it's for

gOSINT bundles several OSINT lookups behind a single `-m` module flag:
harvest commit-author emails from git repositories (GitHub/Bitbucket APIs or
raw clones), check addresses against HaveIBeenPwned, find related identities
on PGP key servers, grep source trees for email addresses, scrape public
Telegram group history, and pull Shodan host reports. The `-f` (full) flag
chains modules — emails found by one module feed the PGP and breach lookups
automatically. API keys (Shodan, HaveIBeenPwned, Hunter) live in
`~/.config/gOSINT.conf`. It is not a general search-engine or social-media
OSINT tool — those modules were planned but never finished, and the project
has been dormant for years, so expect some scrapers to have drifted.

## Typical use cases

### Harvest emails from a git repository and chain the lookups

```
gosint -m git --url https://github.com/example/example-repo --gitAPI github -f
```

Outcome: commits are parsed for author emails, then each address is run
through the PGP and HaveIBeenPwned modules in the same session, printing
key-server identities and breach counts alongside the harvest.

### Check one address against breach dumps

```
gosint -m pwnd --mail user@example.com
```

Outcome: prints the breaches HaveIBeenPwned holds for the address (API key
configured in `~/.config/gOSINT.conf`).

### Grep a source tree or cloned repository for emails

```
gosint -m plainSearch --clone --url https://github.com/example/example-repo
```

Outcome: clones the repository (or scans a local tree via `--path`) and
extracts every email-shaped string found in the code; `--ask-confirmation`
switches to interactive approval before an address joins the result set.

### Scrape a public Telegram group's history

```
gosint -m telegram --tgroup example_group --dumpfile
```

Outcome: walks the group's public message history (bounds via `-s`/`-e`,
rate-limit grace via `--grace`), appending messages to a dumpfile it resumes
from on the next run.

### Pull a Shodan report for a host

```
gosint -m shodan -t 198.51.100.10 --honeypot
```

Outcome: prints Shodan's service report for the host plus a honeypot
probability score (Shodan API key required; `--newscan` forces a fresh scan
at the cost of one scan credit).

*No corpus evidence — derived from upstream documentation.*
