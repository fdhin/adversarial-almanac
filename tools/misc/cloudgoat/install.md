---
tool: "cloudgoat"
build-system: "python"
upstream: "https://github.com/RhinoSecurityLabs/cloudgoat"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing cloudgoat

> Rhino Security Labs' "vulnerable by design" AWS/Azure scenario deployment tool.

Source: cloudpentestcheatsheets feed; upstream [`RhinoSecurityLabs/cloudgoat`](https://github.com/RhinoSecurityLabs/cloudgoat).

## Requirements

- Linux or macOS (Windows not officially supported; tab-completion wants bash 4.2+)
- Python 3.9+
- Terraform >= 1.5.0, AWS CLI, Azure CLI and `jq` installed and on `$PATH`, plus accounts with privileges to create/destroy resources

```bash
sudo apt install terraform awscli azure-cli jq -y
```

## Install with pipx (recommended)

```bash
pipx install cloudgoat
```

## Docker

```bash
docker run -it rhinosecuritylabs/cloudgoat:latest
```

Mount host cloud credentials only if you accept the container having them: `docker run -it -v ~/.aws:/root/.aws/ rhinosecuritylabs/cloudgoat:latest`. A play-with-docker stack file is linked in the upstream README.

## Warnings

- CloudGoat creates intentionally vulnerable resources — never deploy to a production account.
- It only manages resources it creates; remove anything you created yourself before `destroy`.

Backlinks: [[misc]] · [[cloudgoat]]
