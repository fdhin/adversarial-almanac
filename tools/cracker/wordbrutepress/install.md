---
tool: "wordbrutepress"
build-system: "scripts"
upstream: "http://www.homelab.it/index.php/2014/11/03/wordpress-brute-force-multithreading/"
recipe: "blackarch"
source: "http://www.homelab.it/index.php/2014/11/03/wordpress-brute-force-multithreading/"
license: "custom:unknown"
blackarch-pkgver: "30.5165648"
---

# Installing wordbrutepress

> Python script that performs brute forcing against WordPress installs using a wordlist.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wordbrutepress) (version `30.5165648`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wordbrutepress    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.homelab.it/index.php/2014/11/03/wordpress-brute-force-multithreading/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[wordbrutepress]] · [[BlackArch Tools]]

