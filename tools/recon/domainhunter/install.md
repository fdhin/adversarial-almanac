---
tool: "domainhunter"
build-system: "scripts"
upstream: "https://github.com/threatexpress/domainhunter"
recipe: "blackarch"
source: "https://github.com/threatexpress/domainhunter"
license: "BSD"
blackarch-pkgver: "51.38cb7ef"
---

# Installing domainhunter

> Checks expired domains for categorization/reputation and Archive.org history to determine good candidates for phishing and C2 domain names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domainhunter) (version `51.38cb7ef`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S domainhunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/threatexpress/domainhunter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `pillow`, `pytesseract`, `requests`, `texttable`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[domainhunter]] · [[BlackArch Tools]]

