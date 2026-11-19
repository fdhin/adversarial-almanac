---
tool: "redditc2"
build-system: "python"
upstream: "https://github.com/kleiton0x00/redditc2"
recipe: "upstream"
version: "git (2023-01-18)"
license: "see repository"
---

# Installing redditc2

> Abusing Reddit API to host the C2 traffic.

Source: kitploit directory; upstream [`kleiton0x00/redditc2`](https://github.com/kleiton0x00/redditc2).

## Prebuilt / releases

Check [upstream releases](https://github.com/kleiton0x00/redditc2/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/kleiton0x00/redditc2.git
cd redditc2
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[backdoor]] · [[redditc2]]
