---
tool: "rootwire"
build-system: "python"
upstream: "https://github.com/EONRaider/RootWire"
recipe: "upstream"
version: "git (2026-11-07)"
license: "AGPL3"
---

# Installing rootwire

> GNU/Linux traffic monitor — live capture, pcap replay, NDJSON output. Formerly Packet-Sniffer.

Upstream [`EONRaider/RootWire`](https://github.com/EONRaider/RootWire) — the repository was renamed from `eonraider/packet-sniffer`, which still redirects there. Python 3.12+, GNU/Linux only.

## PyPI (upstream-recommended)

```bash
pipx install rootwire        # or: uv tool install rootwire
```

## From a clone (uv)

```bash
git clone https://github.com/EONRaider/RootWire.git
cd RootWire
uv sync
sudo .venv/bin/python -m rootwire -i eth0    # run without installing
```

## Notes

- Live capture (`-i`) needs root or `CAP_NET_RAW` on the interpreter; replaying pcaps (`-r`) needs no privileges.
- Not yet in distro repositories (checked 2026-11-07); PyPI package `rootwire` is the canonical install.

Backlinks: [[sniffer]] · [[rootwire]] · [[packet-sniffer]]
