---
name: "stacs"
categories:
  - blackarch-scanner
version: "0.5.1.r0.g11f3503"
homepage: "https://github.com/stacscan/stacs"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "stacscan/stacs"
github-stars: 95
github-pushed: "2023-05-08"
github-archived: true
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stacs

> YARA-powered scanner for credentials accidentally baked into binary release artifacts.

## What it's for

STACS (Static Token And Credential Scanner) hunts static credentials — API
keys, tokens, key material — inside the binary artifacts teams ship: Docker
images, Android APKs, Java JARs, RPMs, Debian and macOS packages, and archives
nested to arbitrary depth (7z, iso, rar, xz, dmg and more are unpacked
recursively). Detection runs YARA rules grouped into composable rule packs;
findings are emitted as SARIF (or a human-readable tree with `--pretty`), and
false positives can be permanently suppressed through composable ignore lists
that teams share. Reach for it in release and CI pipelines to catch secrets
before they ship. Note that the PyPI package ships without rules — the
community rule pack must be present — and it is aimed at artifacts rather
than plain source trees.

## Typical use cases

### Scan a folder of artifacts with the Docker image

Check a release directory without a local install.

```
docker run --rm -e STACS_OUTPUT_PRETTY=1 -v "$(pwd):/mnt/stacs/input:ro" stacscan/stacs:latest
```

Outcome: everything under the current directory is unpacked and matched
against the image's rule packs; findings print as a readable tree instead of
SARIF JSON (logs go to stderr).

### Scan locally with an explicit rule pack

Run the `stacs` CLI (PyPI package plus the community rules) over specific
paths.

```
stacs --pretty --rule-pack ~/.stacs/pack.json artifacts/
```

Outcome: the given paths are scanned with the configured rule pack; findings
are written to stdout — SARIF by default, human-readable with `--pretty`.

### Triage false positives and survive corrupt archives

Cut recurring benign matches and keep large scans running.

```
stacs --ignore-list ignore.json --skip-unprocessable --threads 20 artifacts/
```

Outcome: findings matched by the ignore list are marked suppressed (with the
ignore reason) in the SARIF output, corrupt or unprocessable archives are
skipped with a warning instead of failing the scan, and processing is spread
across 20 threads.

*No corpus evidence — derived from upstream documentation (stacscan/stacs
README and the click options in stacs/scan/entrypoint/cli.py).*
