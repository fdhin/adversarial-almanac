---
tool: "vlany"
build-system: "scripts"
upstream: "https://github.com/mempodippy/vlany"
recipe: "blackarch"
source: "https://github.com/mempodippy/vlany"
license: "GPL3"
blackarch-pkgver: "255.9ef014a"
---

# Installing vlany

> Linux LD_PRELOAD rootkit (x86 and x86_64 architectures).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vlany) (version `255.9ef014a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S vlany    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mempodippy/vlany) (the recipe uses a versioned archive).

```bash
# quick install (upstream README): fetches the latest tarball into a random
# /tmp dir, runs the installer, then removes the dir
wget https://gist.githubusercontent.com/mempodippy/d93fd99164bace9e63752afb791a896b/raw/6b06d235beac8590f56c47b7f46e2e4fac9cf584/quick_install.sh -O /tmp/quick_install.sh && chmod +x /tmp/quick_install.sh && /tmp/quick_install.sh

# manual: dialog TUI installer
wget https://github.com/mempodippy/vlany/archive/master.tar.gz && tar xvpfz master.tar.gz
cd vlany-master && ./install.sh        # or ./install.sh --cli when dialog is unavailable
```

`install.sh` compiles the rootkit library, prompts for the magic GID, execve/PAM passwords and accept() port range, and writes `/etc/ld.so.preload`. Upstream warnings: experimental — some systems brick on reboot (test on a VM/snapshot); known to fail installing above CentOS 6.6; unmaintained since 2020, with `naworkcaj/bdvl` as the maintained successor.

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[vlany]] · [[BlackArch Tools]]

