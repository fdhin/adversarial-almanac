---
tool: "crackmapexec"
build-system: "scripts"
upstream: "https://github.com/byt3bl33d3r/CrackMapExec"
recipe: "blackarch"
source: "https://github.com/byt3bl33d3r/CrackMapExec"
license: "BSD"
blackarch-pkgver: "v6.0.1.r198.gda472cb"
provides: ["cme", "cmedb"]
---

# Installing crackmapexec

> A swiss army knife for pentesting Windows/Active Directory environments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackmapexec) (version `v6.0.1.r198.gda472cb`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S crackmapexec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/byt3bl33d3r/CrackMapExec) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `netexec`

## Installed layout (per recipe)

- Commands: `/usr/bin/cme`, `/usr/bin/cmedb`

Backlinks: [[scanner]] · [[crackmapexec]] · [[BlackArch Tools]]

