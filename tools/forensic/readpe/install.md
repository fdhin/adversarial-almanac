---
tool: "readpe"
build-system: "make"
upstream: "https://github.com/mentebinaria/readpe/wiki"
recipe: "kali-dsc"
aur-package: "readpe"
version: "0.85.1-1+b2"
---

# Installing readpe

> command-line tools to manipulate Windows PE files

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/readpe) (version `0.85.1-1+b2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install readpe
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss readpe`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/mentebinaria/readpe/wiki
cd wiki
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `dpkg-dev`, `debhelper-compat`, `dh-exec`, `libssl-dev`
- Runtime deps: see [pkg.kali.org/pkg/readpe](https://pkg.kali.org/pkg/readpe)

Backlinks: [[forensic]] · [[readpe]] · [[Kali Gap Analysis]]
