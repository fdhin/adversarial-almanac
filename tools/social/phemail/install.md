---
tool: "phemail"
build-system: "scripts"
upstream: "https://github.com/Dionach/PhEmail"
recipe: "blackarch"
source: "https://github.com/Dionach/PhEmail"
license: "GPL3"
blackarch-pkgver: "28.302b24d"
---

# Installing phemail

> A python open source phishing email tool that automates the process of sending phishing emails as part of a social engineering test.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phemail) (version `28.302b24d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S phemail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Dionach/PhEmail.git
cd PhEmail
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-dnspython (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[phemail]] · [[BlackArch Tools]]

