---
tool: "screenshotbof"
build-system: "make"
upstream: "https://github.com/codextf2/screenshotbof"
recipe: "upstream"
version: "git (2026-07-28)"
license: "see repository"
---

# Installing screenshotbof

> An alternative screenshot capability for Cobalt Strike that uses WinAPI and does not perform a fork & run. Screenshot downloaded in memory.

Source: kitploit directory; upstream [`codextf2/screenshotbof`](https://github.com/codextf2/screenshotbof).

## Prebuilt / releases

Check [upstream releases](https://github.com/codextf2/screenshotbof/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/codextf2/screenshotbof.git
cd screenshotbof
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[scanner]] · [[screenshotbof]]
