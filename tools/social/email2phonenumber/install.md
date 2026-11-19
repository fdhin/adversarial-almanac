---
tool: "email2phonenumber"
build-system: "scripts"
upstream: "https://github.com/martinvigo/email2phonenumber"
recipe: "blackarch"
source: "https://github.com/martinvigo/email2phonenumber"
license: "MIT"
blackarch-pkgver: "29.9df9dbe"
---

# Installing email2phonenumber

> A OSINT tool to obtain a target's phone number just by having his email address.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/email2phonenumber) (version `29.9df9dbe`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S email2phonenumber    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/martinvigo/email2phonenumber) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `bs4`, `certifi`, `chardet`, `idna`, `requests`, `soupsieve`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[email2phonenumber]] · [[BlackArch Tools]]

