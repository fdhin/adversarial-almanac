---
tool: "miredo"
build-system: "autotools"
upstream: "http://www.remlab.net/miredo/"
recipe: "kali-dsc"
nix-package: "miredo"
aur-package: "miredo"
version: "1.2.6-7.2"
---

# Installing miredo

> Teredo IPv6 tunneling through NATs

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/miredo) (version `1.2.6-7.2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install miredo
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss miredo`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#miredo    # ad-hoc run, no install
nix profile install nixpkgs#miredo   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.remlab.net/miredo/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Build dependencies (from source .dsc)

- `automake`, `autopoint`, `debhelper`, `debhelper-compat`, `libcap2-dev`, `libjudy-dev`, `libtool`
- Runtime deps: see [pkg.kali.org/pkg/miredo](https://pkg.kali.org/pkg/miredo)

Backlinks: [[backdoor]] · [[miredo]] · [[Kali Gap Analysis]]
