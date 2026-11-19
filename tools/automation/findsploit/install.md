---
tool: "findsploit"
build-system: "scripts"
upstream: "https://github.com/1N3/findsploit"
recipe: "blackarch"
source: "https://github.com/1N3/findsploit"
license: "custom:unknown"
blackarch-pkgver: "87.3e61d8d"
---

# Installing findsploit

> Find exploits in local and online databases instantly.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/findsploit) (version `87.3e61d8d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S findsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1N3/Findsploit.git
cd Findsploit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `exploitdb`
  - `firefox`
  - `metasploit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[findsploit]] · [[BlackArch Tools]]

