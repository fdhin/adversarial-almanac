---
tool: "git-dump"
build-system: "node"
upstream: "https://github.com/bahamas10/node-git-dump"
recipe: "blackarch"
source: "https://github.com/bahamas10/node-git-dump"
license: "MIT"
blackarch-pkgver: "7.4c9a2a9"
---

# Installing git-dump

> Dump the contents of a remote git repository without directory listing enabled.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/git-dump) (version `7.4c9a2a9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S git-dump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/bahamas10/node-git-dump) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[git-dump]] · [[BlackArch Tools]]

