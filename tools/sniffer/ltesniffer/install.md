---
tool: "ltesniffer"
build-system: "make"
upstream: "https://github.com/syssec-kaist/ltesniffer"
recipe: "upstream"
version: "git (2024-10-23)"
license: "see repository"
---

# Installing ltesniffer

> An Open-source LTE Downlink/Uplink Eavesdropper

Source: kitploit directory; upstream [`syssec-kaist/ltesniffer`](https://github.com/syssec-kaist/ltesniffer).

## Prebuilt / releases

Check [upstream releases](https://github.com/syssec-kaist/ltesniffer/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/syssec-kaist/ltesniffer.git
cd ltesniffer
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[sniffer]] · [[ltesniffer]]
