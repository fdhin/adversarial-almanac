---
tool: "kh2hc"
build-system: "ruby"
upstream: "https://github.com/noraj/kh2hc"
recipe: "blackarch"
source: "https://github.com/noraj/kh2hc"
license: "MIT"
blackarch-pkgver: "0.1.0.r2.g96b9e68"
---

# Installing kh2hc

> Convert OpenSSH known_hosts file hashed with HashKnownHosts to hashes crackable by Hashcat.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kh2hc) (version `0.1.0.r2.g96b9e68`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S kh2hc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/noraj/kh2hc) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ctf-party`
  - `ruby`
  - `ruby-docopt`

## Exact build steps from the recipe

```bash
cd $_gemname

  gem build $_gemname.gemspec
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[kh2hc]] · [[BlackArch Tools]]

