---
tool: "rtl"
build-system: "unknown"
upstream: "https://github.com/merbanan/rtl_433"
recipe: "none"
nix-package: "rtl"
---

# Installing rtl

> A generic software defined radio data receiver, mainly for the 433.92 MHz, 868 MHz (SRD), 315 MHz, 345 MHz, and 915 MHz ISM bands.

⚠️ No BlackArch PKGBUILD exists for this tool (retired from the repo or listed-only).

- Upstream: https://github.com/merbanan/rtl_433
- Check your distro's repos / the AUR (`yay -S rtl`) / upstream README.
- For AI agents: clone upstream and follow its README; treat this tool as possibly unmaintained.

## Nix (works on any distro)

```bash
nix run nixpkgs#rtl    # ad-hoc run, no install
nix profile install nixpkgs#rtl   # or persist it
```
