---
tool: "koadic"
build-system: "scripts"
upstream: "https://github.com/offsecginger/koadic"
recipe: "blackarch"
source: "https://github.com/offsecginger/koadic"
license: "Apache-2.0"
blackarch-pkgver: "3.457f9a3"
---

# Installing koadic

> A Windows post-exploitation rootkit similar to other penetration testing tools such as Meterpreter and Powershell Empire.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/koadic) (version `3.457f9a3`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S koadic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/offsecginger/koadic) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `legacy-cgi`, `pyasn1`, `pycryptodome`, `rjsmin`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[koadic]] · [[BlackArch Tools]]

