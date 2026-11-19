---
tool: "tftpd32"
build-system: "windows"
upstream: "https://tftpd32.jounin.net/tftpd32.html"
recipe: "kali-dsc"
version: "4.50-0kali3"
---

# Installing tftpd32

> Open source ipv6-ready TFTP server for Windows

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/tftpd32) (version `4.50-0kali3`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install tftpd32
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss tftpd32`); otherwise build from source below.

## Any other Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://tftpd32.jounin.net/tftpd32.html
wine <tool>.exe
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/tftpd32](https://pkg.kali.org/pkg/tftpd32)

Backlinks: [[windows]] · [[tftpd32]] · [[Kali Gap Analysis]]
