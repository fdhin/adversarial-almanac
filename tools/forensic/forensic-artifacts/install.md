---
tool: "forensic-artifacts"
build-system: "python"
upstream: "https://github.com/ForensicArtifacts/artifacts"
recipe: "kali-dsc"
version: "20230928-2"
---

# Installing forensic-artifacts

> knowledge base of forensic artifacts (data files)

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/forensic-artifacts) (version `20230928-2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install forensic-artifacts
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss forensic-artifacts`); otherwise build from source below.

## Any other Linux distro

**B. From source:**

```bash
git clone https://github.com/ForensicArtifacts/artifacts
cd artifacts
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `dh-python`, `python3-all`, `python3-setuptools`, `python3-yaml`, `yapf3`
- Runtime deps: see [pkg.kali.org/pkg/forensic-artifacts](https://pkg.kali.org/pkg/forensic-artifacts)

Backlinks: [[forensic]] · [[forensic-artifacts]] · [[Kali Gap Analysis]]
