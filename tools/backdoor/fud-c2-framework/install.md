---
tool: "fud-c2-framework"
build-system: "python"
upstream: "https://github.com/x3r0day/fud-c2-framework"
recipe: "upstream"
version: "git (2026-05-10)"
license: "see repository"
---

# Installing fud-c2-framework

> A fileless reverse shell and C2 framework leveraging direct syscalls, proxy tunneling, and ChaCha20 encryption for AV evasion.

Source: kitploit directory; upstream [`x3r0day/fud-c2-framework`](https://github.com/x3r0day/fud-c2-framework).

## Prebuilt / releases

Check [upstream releases](https://github.com/x3r0day/fud-c2-framework/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/x3r0day/fud-c2-framework.git
cd fud-c2-framework
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[backdoor]] · [[fud-c2-framework]]
