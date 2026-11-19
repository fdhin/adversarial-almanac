---
name: "clair"
categories:
  - blackarch-scanner
version: "2223.4cec58a0"
homepage: "https://github.com/quay/clair"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: APACHE
meta-checked: "2026-11-09"
github-repo: "quay/clair"
github-stars: 11087
github-pushed: "2026-11-04"
github-archived: false
github-issues: 61
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 109
gh-activity-checked: "2026-11-09"
---

# clair

> Static vulnerability-analysis service for container images (OCI and Docker), exposing indexing and matching over an HTTP API.

## What it's for

Clair (Project Quay, origin at CoreOS) is an open-source server that indexes container images and matches their installed packages against vulnerability databases, so registries and CI pipelines can flag vulnerable images before deployment. It is an API service, not a point-and-shoot scanner: you run the `clair` daemon with a YAML config file and clients submit image manifests over HTTP. The config (sample shipped at /etc/clair/config.yaml) selects the mode and the indexer/matcher/notifier components, backed by a Postgres database; Clair periodically updates its vulnerability data. The package also ships `clairctl`, a helper that talks to a Clair API and can generate config. It analyzes container images only — not running hosts, VMs or arbitrary binaries.

No corpus evidence — derived from upstream source and documentation.

## Typical use cases

### Start the Clair API service

Run the daemon against its config file.

```
clair -conf /etc/clair/config.yaml
```

Outcome: Clair validates the YAML, starts the configured indexer/matcher/notifier (or combined mode), begins downloading vulnerability databases, and serves its HTTP API on the configured port (default 6060) for clients to index manifests and fetch vulnerability reports. The config path can equivalently be supplied via the CLAIR_CONF environment variable.
