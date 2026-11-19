---
tool: "sniffle"
build-system: "python"
upstream: "https://github.com/nccgroup/sniffle"
recipe: "upstream"
version: "git (2025-09-25)"
license: "see repository"
---

# Installing sniffle

> A sniffer for Bluetooth 5 and 4.x LE

Source: kitploit directory; upstream [`nccgroup/sniffle`](https://github.com/nccgroup/sniffle).

## Prebuilt / releases

Check [upstream releases](https://github.com/nccgroup/sniffle/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/nccgroup/sniffle.git
cd sniffle
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[wireless]] · [[sniffle]]
