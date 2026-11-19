---
tool: "wpaxfuzz"
build-system: "python"
upstream: "https://github.com/efchatz/wpaxfuzz"
recipe: "upstream"
version: "git (2026-06-15)"
license: "see repository"
---

# Installing wpaxfuzz

> A full-featured open-source Wi-Fi fuzzer

Source: kitploit directory; upstream [`efchatz/wpaxfuzz`](https://github.com/efchatz/wpaxfuzz).

## Prebuilt / releases

Check [upstream releases](https://github.com/efchatz/wpaxfuzz/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/efchatz/wpaxfuzz.git
cd wpaxfuzz
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[fuzzer]] · [[wpaxfuzz]]
