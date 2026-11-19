---
tool: "arachni"
build-system: "scripts"
upstream: "https://www.arachni-scanner.com"
recipe: "blackarch"
source: "https://www.arachni-scanner.com"
license: "APACHE"
blackarch-pkgver: "1.6.1.3.5.g9cd73f254"
---

# Installing arachni

> A feature-full, modular, high-performance Ruby framework aimed towards helping penetration testers and administrators evaluate the security of web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/arachni) (version `1.6.1.3.5.g9cd73f254`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S arachni    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.arachni-scanner.com) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `phantomjs`
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[arachni]] · [[BlackArch Tools]]

