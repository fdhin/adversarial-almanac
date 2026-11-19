---
tool: "frida-ios-dump"
build-system: "scripts"
upstream: "https://github.com/AloneMonkey/frida-ios-dump"
recipe: "blackarch"
source: "https://github.com/AloneMonkey/frida-ios-dump"
license: "MIT"
blackarch-pkgver: "62.8395374"
---

# Installing frida-ios-dump

> Pull decrypted ipa from jailbreak device.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/frida-ios-dump) (version `62.8395374`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S frida-ios-dump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/AloneMonkey/frida-ios-dump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-bcrypt, python2-cffi, python2-colorama, python2-enum34, python2-frida-tools, python2-idna, python2-ipaddress, python2-paramiko, python2-prompt_toolkit, python2-pyasn1, python2-pycparser, python2-pygments, python2-pynacl, python2-scp, python2-six, python2-tqdm, python2-wcwidth (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[frida-ios-dump]] · [[BlackArch Tools]]

