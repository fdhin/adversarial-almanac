---
tool: "creds"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/creds.py"
recipe: "blackarch"
nix-package: "creds"
source: "https://github.com/DanMcInerney/creds.py"
license: "custom"
blackarch-pkgver: "17.1ec8297"
provides: ["creds-py"]
---

# Installing creds

> Harvest FTP/POP/IMAP/HTTP/IRC credentials along with interesting data from each of the protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/creds) (version `17.1ec8297`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S creds    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DanMcInerney/creds.py.git
cd creds.py
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/creds-py`

Backlinks: [[sniffer]] · [[creds]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#creds    # ad-hoc run, no install
nix profile install nixpkgs#creds   # or persist it
```
