---
tool: "rephrase"
build-system: "other"
upstream: "https://www.roguedaemon.net/rephrase/"
recipe: "kali-dsc"
aur-package: "rephrase"
version: "0.2-4"
---

# Installing rephrase

> Specialized passphrase recovery tool for GnuPG

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/rephrase) (version `0.2-4`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install rephrase
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rephrase`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.roguedaemon.net/rephrase/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/rephrase](https://pkg.kali.org/pkg/rephrase)

Backlinks: [[forensic]] · [[rephrase]] · [[Kali Gap Analysis]]
