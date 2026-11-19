---
tool: "gitrob"
build-system: "go"
upstream: "http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/"
recipe: "blackarch"
aur-package: "gitrob"
source: "http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/"
license: "custom:unknown"
blackarch-pkgver: "7.7be4c53"
---

# Installing gitrob

> Reconnaissance tool for GitHub organizations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitrob) (version `7.7be4c53`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gitrob    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint//releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `go-pie`

## Exact build steps from the recipe

```bash
# Repo is unmaintained (archived), does not accept the new build process
  mkdir -p "src/$url" && rm -rf "src/$url" && mv $pkgname "src/$url" &&
    cd "src/$url"

  GOPATH="$srcdir" go get -d -t $url
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gitrob]] · [[BlackArch Tools]]

