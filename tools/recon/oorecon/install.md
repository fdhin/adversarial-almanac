---
tool: "oorecon"
build-system: "python"
upstream: "https://github.com/hilmiazizi/oorecon"
recipe: "upstream"
version: "git (2026-09-28)"
license: "see repository"
---

# Installing oorecon

> Terminal UI for attack surface discovery: DNS recon, subdomain enumeration, port scanning, reverse IP, leaked login URLs, and JavaScript secret mining.

Source: kitploit directory; upstream [`hilmiazizi/oorecon`](https://github.com/hilmiazizi/oorecon).

## Prebuilt / releases

Check [upstream releases](https://github.com/hilmiazizi/oorecon/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/hilmiazizi/oorecon.git
cd oorecon
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[recon]] · [[oorecon]]
