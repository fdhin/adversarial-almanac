---
tool: "ip2provider"
build-system: "python"
upstream: "https://github.com/oldrho/ip2provider"
recipe: "upstream"
license: "GPL3"
---

# Installing ip2provider

> Maps IPs to their hosting cloud provider from local route-range lists.

Source: cloudpentestcheatsheets feed; upstream [`oldrho/ip2provider`](https://github.com/oldrho/ip2provider).

## Install from source

```bash
git clone https://github.com/oldrho/ip2provider.git
cd ip2provider
pip3 install -r requirements.txt
```

## Usage

The tool is a single script: `./ip2provider.py [flags] [ip]` — targets can also be piped in, one IP per line. Run with `--update-lists` to pull fresh provider range lists before lookups.

Backlinks: [[recon]] · [[ip2provider]]
