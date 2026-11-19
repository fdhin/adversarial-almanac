---
tool: "pdfwalker"
build-system: "ruby"
upstream: "https://github.com/gdelugre/pdfwalker"
recipe: "blackarch"
source: "https://github.com/gdelugre/pdfwalker"
license: "GPL3"
blackarch-pkgver: "7.64c17f0"
---

# Installing pdfwalker

> Frontend to explore the internals of a PDF document with Origami

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdfwalker) (version `7.64c17f0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pdfwalker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/gdelugre/pdfwalker) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `origami`
  - `ruby`
  - `ruby-gtk2`
  - `ruby-ruby-gtkhex`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build "$pkgname.gemspec"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pdfwalker]] · [[BlackArch Tools]]

