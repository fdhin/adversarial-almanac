---
tool: "cloudfox"
build-system: "go"
upstream: "https://github.com/BishopFox/cloudfox"
recipe: "upstream"
license: "MIT"
---

# Installing cloudfox

> Situational-awareness CLI for cloud pentests — AWS, Azure and GCP attack-path discovery.

Source: cloudpentestcheatsheets feed; upstream [`BishopFox/cloudfox`](https://github.com/BishopFox/cloudfox).

## Prebuilt binary (recommended)

Download the latest release for your platform from the [Releases](https://github.com/BishopFox/cloudfox/releases) page. Use v1.17.0 or greater — earlier versions stopped working after a format change in AWS's public service mapping file (12/2025).

## Homebrew

```bash
brew install cloudfox
```

## Install with Go

```bash
go install github.com/BishopFox/cloudfox@latest
```

## Build from source

```bash
git clone https://github.com/BishopFox/cloudfox.git
cd cloudfox
go build .
./cloudfox
```

## Prerequisites

- AWS: AWS CLI installed; auth via profiles, environment variables, or EC2 metadata. Recommended attached policies: `SecurityAudit` plus the upstream [cloudfox custom policy](https://github.com/BishopFox/cloudfox/blob/master/misc/aws/cloudfox-policy.json) (the custom policy alone covers every permission cloudfox uses and nothing else).
- Azure: `az` CLI session with Viewer or similar permissions.
- GCP: Google Cloud SDK installed and authenticated, Application Default Credentials configured (`gcloud auth application-default login`), and the relevant project APIs enabled (Cloud Asset, Cloud Identity, IAM, Compute, ...). See the upstream GCP Setup Guide wiki page for the role hierarchy list.

## Notes

- Errors from individual commands are usually missing permissions or region-unsupported services; details land in `~/.cloudfox/cloudfox-error.log`.
- Full command documentation lives in the [project wiki](https://github.com/BishopFox/cloudfox/wiki).

Backlinks: [[recon]] · [[cloudfox]]
