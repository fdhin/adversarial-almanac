---
tool: "apkinspector"
build-system: "python"
upstream: "https://github.com/erev0s/apkInspector"
recipe: "upstream"
license: "Apache-2.0"
---

# Installing apkinspector

> Parses APK zip structure per the spec itself — lists, extracts and decodes even APKs built to evade standard unzip/analysis libraries.

Source: hacktricks feed; upstream [`erev0s/apkInspector`](https://github.com/erev0s/apkInspector).

## From PyPI

```bash
pip install apkInspector
```

## Build from source (poetry)

```bash
git clone https://github.com/erev0s/apkInspector.git
cd apkInspector
poetry build
pip install dist/apkInspector-*.tar.gz
```

## Not yet packaged

No distro packages this tool (checked 2026-11-13) — use pip.

Backlinks: [[mobile]] · [[apkinspector]]
