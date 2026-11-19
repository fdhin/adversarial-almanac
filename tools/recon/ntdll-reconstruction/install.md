---
tool: "ntdll-reconstruction"
build-system: "make"
upstream: "https://github.com/sk4r4b/ntdll-reconstruction"
recipe: "upstream"
version: "git (2026-10-28)"
license: "see repository"
---

# Installing ntdll-reconstruction

> A technique that mixes NTDLL unhooking and hellsgate gate varients

Source: kitploit directory; upstream [`sk4r4b/ntdll-reconstruction`](https://github.com/sk4r4b/ntdll-reconstruction).

## Prebuilt / releases

Check [upstream releases](https://github.com/sk4r4b/ntdll-reconstruction/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/sk4r4b/ntdll-reconstruction.git
cd ntdll-reconstruction
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[recon]] · [[ntdll-reconstruction]]
