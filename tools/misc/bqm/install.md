---
tool: "bqm"
build-system: "ruby"
upstream: "https://github.com/Acceis/bqm"
recipe: "blackarch"
aur-package: "bqm"
source: "https://github.com/Acceis/bqm"
license: "MIT"
blackarch-pkgver: "v1.5.1.r5.ge287280"
---

# Installing bqm

> Download BloudHound query lists, deduplicate entries and merge them in one file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bqm) (version `v1.5.1.r5.ge287280`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S bqm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/Acceis/bqm) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Exact build steps from the recipe

```bash
cd $_gemname

  gem build "$_gemname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[bqm]] · [[BlackArch Tools]]

