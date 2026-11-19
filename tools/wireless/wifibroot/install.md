---
tool: "wifibroot"
build-system: "scripts"
upstream: "https://github.com/hash3liZer/WiFiBroot"
recipe: "blackarch"
source: "https://github.com/hash3liZer/WiFiBroot"
license: "GPL3"
blackarch-pkgver: "84.d0cd2cc"
---

# Installing wifibroot

> A WiFi Pentest Cracking tool for WPA/WPA2 (Handshake, PMKID, Cracking, EAPOL, Deauthentication).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifibroot) (version `84.d0cd2cc`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wifibroot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hash3liZer/WiFiBroot.git
cd WiFiBroot
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-typing, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifibroot]] · [[BlackArch Tools]]

