---
tool: "gittools"
build-system: "scripts"
upstream: "https://github.com/internetwache/GitTools"
recipe: "blackarch"
source: "https://github.com/internetwache/GitTools"
license: "custom:unknown"
blackarch-pkgver: "78.8fa09eb"
provides: ["gittools-extractor", "gittools-gitdumper", "gittools-gitfinder"]
---

# Installing gittools

> A repository with 3 tools for pwn'ing websites with .git repositories available'.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gittools) (version `78.8fa09eb`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gittools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/internetwache/GitTools.git
cd GitTools
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/gittools-extractor`, `/usr/bin/gittools-gitdumper`, `/usr/bin/gittools-gitfinder`

Backlinks: [[webapp]] · [[gittools]] · [[BlackArch Tools]]

