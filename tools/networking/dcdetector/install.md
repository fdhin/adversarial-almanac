---
tool: "dcdetector"
build-system: "ruby"
upstream: "https://github.com/noraj/DCDetector"
recipe: "blackarch"
source: "https://github.com/noraj/DCDetector"
license: "MIT"
blackarch-pkgver: "0.0.1.r52.g2e69244"
---

# Installing dcdetector

> Spot all domain controllers in a Microsoft Active Directory environment. Find computer name, FQDN, and IP address(es) of all DCs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dcdetector) (version `0.0.1.r52.g2e69244`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dcdetector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/noraj/DCDetector) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-docopt`
  - `ruby-paint`

## Exact build steps from the recipe

```bash
cd $_gemname

  gem build "$pkgname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dcdetector]] · [[BlackArch Tools]]

