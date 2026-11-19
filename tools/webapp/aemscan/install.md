---
tool: "aemscan"
build-system: "python"
upstream: "https://github.com/Raz0r/aemscan"
recipe: "upstream"
license: "MIT"
---

# Installing aemscan

> Adobe Experience Manager vulnerability scanner — default creds, error-page info leak, WebDav, version detection, useful paths.

Source: howtohunt feed; upstream [`Raz0r/aemscan`](https://github.com/Raz0r/aemscan).

## From source

```bash
git clone https://github.com/Raz0r/aemscan.git
cd aemscan
sudo python setup.py install
aemscan https://aem.example.com
```

Outcome of the install step: registers the `aemscan` console command via
setuptools, ready to run as shown in the README (`aemscan <url>`).

## Not packaged

No distro package ships aemscan (checked 2026-11-13).

Backlinks: [[windows]] · [[aemscan]]
