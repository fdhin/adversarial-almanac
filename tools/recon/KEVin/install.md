---
tool: "KEVin"
build-system: "go"
upstream: "https://github.com/synfinner/kevin"
recipe: "upstream"
version: "git (2026-11-04)"
license: "see repository"
---

# Installing KEVin

> REST API over the CISA Known Exploited Vulnerabilities catalog, exposing CVE lookups, recent KEV additions, text search, and merged CISA, MITRE, and NVD data.

Source: kitploit feature [post](https://kitploit.com/en/tools/github/synfinner/kevin) — upstream [`synfinner/kevin`](https://github.com/synfinner/kevin).

## Prebuilt / releases

Check [upstream releases](https://github.com/synfinner/kevin/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/synfinner/kevin.git
cd kevin
go build ./...
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07) — available via AUR.

Backlinks: [[recon]] · [[KEVin]]
