---
tool: "tnscmd10g"
build-system: "scripts"
upstream: "http://www.red-database-security.com/"
recipe: "kali-dsc"
aur-package: "tnscmd10g"
version: "1.3-1kali3"
---

# Installing tnscmd10g

> Tool to prod the oracle tnslsnr process

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/tnscmd10g) (version `1.3-1kali3`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install tnscmd10g
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss tnscmd10g`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.red-database-security.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/tnscmd10g](https://pkg.kali.org/pkg/tnscmd10g)

Backlinks: [[database]] · [[tnscmd10g]] · [[Kali Gap Analysis]]
