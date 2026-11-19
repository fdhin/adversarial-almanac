---
tool: "reconness"
build-system: "make"
upstream: "https://github.com/reconness/reconness"
recipe: "upstream"
version: "git (2026-10-12)"
license: "see repository"
---

# Installing reconness

> ReconNess is a platform to allow continuous recon (CR) where you can set up a pipeline of #recon tools (Agents) and trigger it base on schedule or events.

Source: kitploit directory; upstream [`reconness/reconness`](https://github.com/reconness/reconness).

## Prebuilt / releases

Check [upstream releases](https://github.com/reconness/reconness/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/reconness/reconness.git
cd reconness
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[ai]] · [[reconness]]
