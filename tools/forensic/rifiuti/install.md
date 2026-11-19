---
tool: "rifiuti"
build-system: "other"
upstream: "https://sf.net/projects/odessa"
recipe: "kali-dsc"
nix-package: "rifiuti"
version: "20040505-4"
---

# Installing rifiuti

> MS Windows recycle bin analysis tool

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/rifiuti) (version `20040505-4`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install rifiuti
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rifiuti`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#rifiuti    # ad-hoc run, no install
nix profile install nixpkgs#rifiuti   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sf.net/projects/odessa) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/rifiuti](https://pkg.kali.org/pkg/rifiuti)

Backlinks: [[forensic]] · [[rifiuti]] · [[Kali Gap Analysis]]
