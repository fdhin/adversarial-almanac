---
tool: "jira-lens"
build-system: "python"
upstream: "https://github.com/MayankPandey01/Jira-Lens"
recipe: "upstream"
license: "MIT"
---

# Installing jira-lens

> Fast and customizable vulnerability scanner for Atlassian Jira (25+ checks per instance).

Source: howtohunt feed; upstream [`MayankPandey01/Jira-Lens`](https://github.com/MayankPandey01/Jira-Lens). Python 3 only (3.8.x recommended).

## pip

```bash
pip install Jira-Lens
```

then complete the setup with `python3 setup.py install` from a clone if the
console entry point is needed.

## From source

```bash
git clone https://github.com/MayankPandey01/Jira-Lens.git
cd Jira-Lens
python3 setup.py install
```

## Docker

```bash
git clone https://github.com/MayankPandey01/Jira-Lens.git
cd Jira-Lens
docker build . -t jira-lens
docker run -v "$(PWD)/Jira-Lens/output":/app/Jira-Lens/output jira-lens <url>
```

## Not packaged

No distro package ships jira-lens (checked 2026-11-13).

Backlinks: [[recon]] · [[jira-lens]]
