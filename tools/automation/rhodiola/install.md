---
tool: "rhodiola"
build-system: "scripts"
upstream: "https://github.com/utkusen/rhodiola"
recipe: "blackarch"
source: "https://github.com/utkusen/rhodiola"
license: "BSD"
blackarch-pkgver: "4.8bc08a0"
---

# Installing rhodiola

> Personalized wordlist generator with NLP, by analyzing tweets (A.K.A crunch2049).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rhodiola) (version `4.8bc08a0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S rhodiola    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/utkusen/rhodiola) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nltk-data`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-colorama, python2-exrex, python2-gensim, python2-geotext, python2-hurry-filesize, python2-nltk, python2-requests, python2-textblob, python2-tweepy, python2-wikipedia (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[rhodiola]] · [[BlackArch Tools]]

