---
name: "grype"
categories:
  - blackarch-scanner
version: "0.33.1"
homepage: "https://github.com/anchore/grype"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-03-05"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "anchore/grype"
github-stars: 13204
github-pushed: "2026-11-07"
github-archived: false
github-issues: 450
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 459
gh-activity-checked: "2026-11-09"
---

# grype

> Anchore's vulnerability scanner for container images, filesystems and SBOMs: inventories the software inside a target and matches it against a CVE database.

## What it's for

grype (Anchore) takes a scan target — a container image from a registry, a local filesystem
directory, an image in containers-storage, or an SBOM — inventories the software in it (OS
packages across Alpine/Debian/RHEL-family ecosystems plus language packages from Ruby, Java,
JavaScript, Python, Go and more) and matches it against known vulnerabilities, with EPSS and KEV
context available for prioritizing what matters. It is the standard building block for
vulnerability scanning in CI pipelines and container hygiene: point it at what you ship and get
a per-package table of CVEs. It finds and reports; remediation and policy decisions (failing a
build on severity) belong to the pipeline around it.

## Typical use cases

### Scan a container image

Check a registry image for known vulnerabilities.

```
grype alpine:latest
```

Outcome: pulls the image, inventories its packages and prints a table of matches — severity,
package name and version, and the vulnerability IDs with links, one row per finding.

### Scan a filesystem

Audit a project directory or an extracted root filesystem.

```
grype ./my-project
```

Outcome: same matching against the packages found in the local tree (language manifests,
lockfiles, binaries), no container runtime involved.

### Scan an SBOM

Skip re-inspection by scanning an existing software bill of materials.

```
grype sbom:./sbom.json
```

Outcome: matches the Syft-generated SBOM against the vulnerability database directly — the
fastest path; `cat ./sbom.json | grype` does the same with the document piped on stdin.

### Scan an image in local containers-storage

Audit an image built or present locally with Buildah/Podman.

```
grype --from containers-storage localhost/myimage:latest
```

Outcome: reads the image from the local containers-storage store instead of a registry — for
rootless stores, run it inside the builder's user namespace (`podman unshare grype ...`).

*No corpus evidence — derived from upstream documentation (anchore/grype README).*
