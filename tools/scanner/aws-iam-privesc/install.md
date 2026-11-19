---
tool: "aws-iam-privesc"
build-system: "scripts"
upstream: "https://github.com/cyberqueenmeg/aws_iam_privesc"
recipe: "blackarch"
source: "https://github.com/cyberqueenmeg/aws_iam_privesc"
license: "custom:unknown"
blackarch-pkgver: "11.2983efd"
---

# Installing aws-iam-privesc

> AWS IAM policy scanner that helps determine where privilege escalation can be achieved.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aws-iam-privesc) (version `11.2983efd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S aws-iam-privesc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cyberqueenmeg/aws_iam_privesc) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `boto3`, `botocore`, `pycryptodome`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aws-cli`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[aws-iam-privesc]] · [[BlackArch Tools]]

