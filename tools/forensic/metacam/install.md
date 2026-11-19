---
tool: "metacam"
build-system: "other"
upstream: "http://www.cheeseplant.org/~daniel/pages/metacam.html"
recipe: "kali-dsc"
version: "1.2-15"
---

# Installing metacam

> extract EXIF information from digital camera files

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/metacam) (version `1.2-15`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install metacam
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss metacam`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cheeseplant.org/~daniel/pages/metacam.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/metacam](https://pkg.kali.org/pkg/metacam)

Backlinks: [[forensic]] · [[metacam]] · [[Kali Gap Analysis]]
