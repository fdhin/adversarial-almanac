---
tool: "wordlists"
build-system: "scripts"
upstream: "https://www.kali.org"
recipe: "kali-dsc"
nix-package: "wordlists"
aur-package: "wordlists"
version: "2026.2.0"
---

# Installing wordlists

> Contains the rockyou wordlist

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/wordlists) (version `2026.2.0`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install wordlists
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss wordlists`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#wordlists    # ad-hoc run, no install
nix profile install nixpkgs#wordlists   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.kali.org) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/wordlists](https://pkg.kali.org/pkg/wordlists)

Backlinks: [[cracker]] · [[wordlists]] · [[Kali Gap Analysis]]
