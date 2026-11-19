---
tool: "wififorge"
build-system: "python"
upstream: "https://github.com/blackhillsinfosec/wififorge"
recipe: "upstream"
version: "git (2026-11-02)"
license: "see repository"
---

# Installing wififorge

> WifiForge is a tool developed by Black Hills InfoSec to help train Pentesters on different Wi-Fi attack vectors and Wireless capabilities.

Source: kitploit directory; upstream [`blackhillsinfosec/wififorge`](https://github.com/blackhillsinfosec/wififorge).

## Prebuilt / releases

Check [upstream releases](https://github.com/blackhillsinfosec/wififorge/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/blackhillsinfosec/wififorge.git
cd wififorge
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[wireless]] · [[wififorge]]
