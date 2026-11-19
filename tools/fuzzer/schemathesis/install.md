---
tool: "schemathesis"
build-system: "python"
upstream: "https://github.com/schemathesis/schemathesis"
recipe: "upstream"
version: "git (2026-11-07)"
license: "BSD-2-Clause"
pypi: "schemathesis"
---

# Installing schemathesis

> Property-based API testing tool: fuzzes REST/GraphQL APIs from OpenAPI schemas to find crashes, auth flaws, and data inconsistencies.

## Any distro with Python (PyPI)

```bash
pipx install schemathesis
```

## From source

```bash
git clone https://github.com/schemathesis/schemathesis.git
cd schemathesis
python3 -m pip install --user .
```

## Not yet packaged

Not in Arch/BlackArch, Kali, AUR, or nixpkgs (checked 2026-11-07); PyPI is the canonical install.

Backlinks: [[fuzzer]] · [[schemathesis]]
