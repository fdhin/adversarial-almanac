---
tool: "gocabrito"
build-system: "scripts"
upstream: "https://github.com/KINGSABRI/goCabrito"
recipe: "blackarch"
source: "https://github.com/KINGSABRI/goCabrito"
license: "MIT"
blackarch-pkgver: "4.33ac59a"
---

# Installing gocabrito

> Super organized and flexible script for sending phishing campaigns.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gocabrito) (version `4.33ac59a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gocabrito    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/KINGSABRI/goCabrito.git
cd goCabrito
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`
  - `ruby-json`
  - `ruby-mail`
  - `ruby-sqlite3`
  - `sqlite3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[gocabrito]] · [[BlackArch Tools]]

