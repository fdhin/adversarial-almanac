---
tool: "pcapxray"
build-system: "scripts"
upstream: "https://github.com/Srinivas11789/PcapXray"
recipe: "blackarch"
source: "https://github.com/Srinivas11789/PcapXray"
license: "MIT"
blackarch-pkgver: "v5.2.r1.ga454d12"
---

# Installing pcapxray

> A Network Forensics Tool - To visualize a Packet Capture offline as a Network Diagram including device identification, highlight important communication and file extraction.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcapxray) (version `v5.2.r1.ga454d12`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pcapxray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Srinivas11789/PcapXray.git
cd PcapXray
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cryptography`, `graphviz`, `ipwhois`, `matplotlib`, `netaddr`, `networkx`, `pillow`, `pyshark`, `pyvis`, `stem`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`
  - `tk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pcapxray]] · [[BlackArch Tools]]

