---
tool: "watobo"
build-system: "ruby"
upstream: "https://sourceforge.net/projects/watobo/"
recipe: "kali-dsc"
version: "1.0.1-0kali2"
---

# Installing watobo

> Semi-automated web application scanner

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/watobo) (version `1.0.1-0kali2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install watobo
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss watobo`); otherwise build from source below.

## Any other Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/watobo/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `gem2deb`
- Runtime deps: see [pkg.kali.org/pkg/watobo](https://pkg.kali.org/pkg/watobo)

Backlinks: [[webapp]] · [[watobo]] · [[Kali Gap Analysis]]
