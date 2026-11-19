---
tool: "commonspeak"
build-system: "go"
upstream: "https://github.com/assetnote/commonspeak2"
recipe: "blackarch"
source: "https://github.com/assetnote/commonspeak2"
license: "Apache"
blackarch-pkgver: "46.ba24110"
---

# Installing commonspeak

> Leverages publicly available datasets from Google BigQuery to generate wordlists.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/commonspeak) (version `46.ba24110`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S commonspeak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/assetnote/commonspeak2/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/assetnote/commonspeak2.git
cd commonspeak2
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `go-pie`
  - `golang-glide`

## Exact build steps from the recipe

```bash
mkdir -p "src/$_url" && rm -rf "src/$_url" && mv $pkgname "src/$_url" &&
    cd "src/$_url"

  GOPATH="$srcdir" glide-go up
  GOPATH="$srcdir" glide-go install
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[commonspeak]] · [[BlackArch Tools]]

