---
tool: "pwcrack"
build-system: "ruby"
upstream: "https://github.com/L-codes/pwcrack-framework"
recipe: "blackarch"
source: "https://github.com/L-codes/pwcrack-framework"
license: "GPL3"
blackarch-pkgver: "368.b6fba8a"
---

# Installing pwcrack

> Password hash automatic cracking framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwcrack) (version `368.b6fba8a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pwcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/L-codes/pwcrack-framework) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pwcrack]] · [[BlackArch Tools]]

