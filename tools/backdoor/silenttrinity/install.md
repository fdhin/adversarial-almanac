---
tool: "silenttrinity"
build-system: "scripts"
upstream: "https://github.com/byt3bl33d3r/SILENTTRINITY"
recipe: "blackarch"
source: "https://github.com/byt3bl33d3r/SILENTTRINITY"
license: "GPL3"
blackarch-pkgver: "293.08b1c61"
---

# Installing silenttrinity

> An asynchronous, collaborative post-exploitation agent powered by Python and .NET's DLR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/silenttrinity) (version `293.08b1c61`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S silenttrinity    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/byt3bl33d3r/SILENTTRINITY.git
cd SILENTTRINITY
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiofiles`, `aiosqlite`, `aiowinreg`, `asciitree`, `asn1crypto`, `blinker`, `certifi`, `cffi`, `chardet`, `click`, `cryptography`, `defusedxml`, `dnslib`, `docopt`, `h11`, `h2`, `hpack`, `hyperframe`, `idna`, `itsdangerous`, `jinja`, `ldap3`, `markupsafe`, `minidump`, `minikerberos`, `msldap`, `multidict`, `netifaces`, `prompt_toolkit`, `pyasn1`, `pycparser`, `pypykatz`, `pytoml`, `quart`, `requests`, `six`, `socks5line`, `sortedcontainers`, `termcolor`, `terminaltables`, `typing_extensions`, `urllib3`, `wcwidth`, `websockets`, `winsspi`, `wsproto`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `hypercorn`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[silenttrinity]] · [[BlackArch Tools]]

