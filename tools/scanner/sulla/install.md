---
tool: "sulla"
build-system: "go"
upstream: "https://github.com/praetorian-inc/Sulla"
recipe: "upstream"
license: "Apache2"
---

# Installing sulla

> SMB share discovery + Titus secret scanning across Active Directory.

Source: HackTricks feed admission; upstream [`praetorian-inc/Sulla`](https://github.com/praetorian-inc/Sulla).

## Prebuilt binaries (releases)

```bash
# Linux x86_64
wget -O sulla https://github.com/praetorian-inc/Sulla/releases/latest/download/sulla-linux-amd64
chmod +x sulla

# Linux ARM64
wget -O sulla https://github.com/praetorian-inc/Sulla/releases/latest/download/sulla-linux-arm64
```

## Docker

```bash
docker pull ghcr.io/praetorian-inc/sulla:latest
docker run --rm --privileged --network=host \
  -v $(pwd):/sulla_output -w /sulla_output \
  ghcr.io/praetorian-inc/sulla:latest \
  -u admin -p secret123 -d corp.local -o results -of txt,json
```

## Not yet packaged

No distro packages Sulla — use release binaries or the container image.
Checked 2026-11-13.

Backlinks: [[scanner]] · [[sulla]]
