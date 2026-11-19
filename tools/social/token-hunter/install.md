---
tool: "token-hunter"
build-system: "scripts"
upstream: "https://github.com/GitLab-Red-Team/token-hunter"
recipe: "blackarch"
source: "https://github.com/GitLab-Red-Team/token-hunter"
license: "BSD"
blackarch-pkgver: "343.3358a33"
---

# Installing token-hunter

> OSINT Tool - Search the group and group members' snippets, issues, and issue discussions for sensitive data that may be included in these assets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/token-hunter) (version `343.3358a33`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S token-hunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GitLab-Red-Team/token-hunter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `coloredlogs`, `furl`, `requests`, `requests-mock`, `retry`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[token-hunter]] · [[BlackArch Tools]]

