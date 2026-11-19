---
tool: "slackpirate"
build-system: "scripts"
upstream: "https://github.com/emtunc/SlackPirate"
recipe: "blackarch"
source: "https://github.com/emtunc/SlackPirate"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.20.r6.g99e470f"
---

# Installing slackpirate

> Slack Enumeration and Extraction Tool - extract sensitive information from a Slack Workspace.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slackpirate) (version `0.20.r6.g99e470f`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S slackpirate    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/emtunc/SlackPirate.git
cd SlackPirate
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`, `termcolor`
- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[slackpirate]] · [[BlackArch Tools]]

