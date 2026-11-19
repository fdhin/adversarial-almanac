---
tool: "secure-socket-funneling-windows-binaries"
build-system: "windows"
upstream: "https://github.com/securesocketfunneling/ssf"
recipe: "kali-dsc"
version: "3.0.0-0kali4"
---

# Installing secure-socket-funneling-windows-binaries

> SSF - windows binaries

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/secure-socket-funneling-windows-binaries) (version `3.0.0-0kali4`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install secure-socket-funneling-windows-binaries
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss secure-socket-funneling-windows-binaries`); otherwise build from source below.

## Any other Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/securesocketfunneling/ssf
wine <tool>.exe
```

## Build dependencies (from source .dsc)

- `debhelper`, `unzip`, `cmake`, `libkrb5-dev`, `libssl1.0-dev`, `python`
- Runtime deps: see [pkg.kali.org/pkg/secure-socket-funneling-windows-binaries](https://pkg.kali.org/pkg/secure-socket-funneling-windows-binaries)

Backlinks: [[windows]] · [[secure-socket-funneling-windows-binaries]] · [[Kali Gap Analysis]]
