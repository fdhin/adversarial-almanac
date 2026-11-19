---
tool: "pywhisker"
build-system: "python"
upstream: "https://github.com/ShutdownRepo/pywhisker"
recipe: "upstream"
license: "see repository"
---

# Installing pywhisker

> Python tool for Shadow Credentials: add, remove and manage `msDS-KeyCredentialLink` entries on AD accounts.

Source: hacktricks feed; upstream [`ShutdownRepo/pywhisker`](https://github.com/ShutdownRepo/pywhisker).

## From source

```bash
git clone https://github.com/ShutdownRepo/pywhisker.git
cd pywhisker
python3 -m pip install -r requirements.txt
python3 pywhisker.py --help
```

Dependencies: Impacket, PyDSInternals (both pulled by
`requirements.txt`).

## Any other Linux distro

Same as above — a plain Python 3 script plus requirements; there is no
PyPI release, so install via git clone only.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[pywhisker]]
