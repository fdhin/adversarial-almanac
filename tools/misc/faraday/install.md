---
tool: "faraday"
build-system: "python"
upstream: "https://faradaysec.com"
recipe: "kali-dsc"
nix-package: "faraday"
version: "5.25.0-0kali1"
---

# Installing faraday

> Collaborative Penetration Test IDE

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/faraday) (version `5.25.0-0kali1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install faraday
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss faraday`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#faraday    # ad-hoc run, no install
nix profile install nixpkgs#faraday   # or persist it
```

## Any other Linux distro

**B. From source:**

**Source:** download the release from the [upstream site](https://faradaysec.com) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `dh-python`, `pybuild-plugin-pyproject`, `python3-all`, `python3-alembic`, `python3-apispec`, `python3-apispec-webframeworks`, `python3-autobahn`, `python3-bcrypt`, `python3-bleach`, `python3-celery`, `python3-croniter`, `python3-cvss`, `python3-faraday-agent-parameters-types`
- Runtime deps: see [pkg.kali.org/pkg/faraday](https://pkg.kali.org/pkg/faraday)

Backlinks: [[misc]] · [[faraday]] · [[Kali Gap Analysis]]
