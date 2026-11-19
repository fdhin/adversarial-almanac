---
tool: "tweets-analyzer"
build-system: "scripts"
upstream: "https://github.com/x0rz/tweets_analyzer"
recipe: "blackarch"
source: "https://github.com/x0rz/tweets_analyzer"
license: "GPL3"
blackarch-pkgver: "55.8d6bd3c"
---

# Installing tweets-analyzer

> Tweets metadata scraper & activity analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tweets-analyzer) (version `55.8d6bd3c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tweets-analyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/x0rz/tweets_analyzer.git
cd tweets_analyzer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-ascii_graph, python2-numpy, python2-tqdm, python2-tweepy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[tweets-analyzer]] · [[BlackArch Tools]]

