---
name: "cloudlist"
categories:
  - blackarch-recon
version: "1013.e198d15"
homepage: "https://github.com/projectdiscovery/cloudlist"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/cloudlist"
github-stars: 1058
github-pushed: "2026-11-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 181
gh-activity-checked: "2026-11-09"
---

# cloudlist

> Inventory hosts and IPs across every cloud account from one config file — attack-surface management for defenders.

## What it's for

cloudlist (ProjectDiscovery) enumerates assets — hostnames and IPs — across a wide set of cloud and infrastructure providers: AWS, GCP, Azure, Kubernetes, DigitalOcean, Linode, Fastly, Cloudflare, Hetzner, Alibaba, OpenStack, SCW, Nomad, Consul, Heroku, Terraform state, Namecheap and more. Provider credentials live in a YAML config (default `~/.config/cloudlist/provider-config.yaml`); keyless authentication from environment credentials (AWS IRSA / instance profiles, GCP workload identity) is available through auto-discovery. Results can be filtered by provider, service (`s3`, `ec2`, `lambda`, `cloudfront`, ...) and host-versus-IP type, with plain, JSON or silent output designed for pipelines. Built for blue teams keeping a centralized asset list; equally useful for mapping a client's real footprint before a pentest.

## Typical use cases

### List every configured asset

Dump the full inventory across all providers in the config.

```
cloudlist
```

Outcome: walks every provider in provider-config.yaml and prints discovered assets tagged by provider and service — an immediate view of what the organization actually runs.

### Filter to one provider and service set

Narrow output to the assets that matter for the current task.

```
cloudlist -p aws -s s3,ec2 -host
```

Outcome: restricts enumeration to AWS S3 buckets and EC2 instances and prints hostnames only — a clean list to feed into further testing or review.

### Auto-discover providers from the environment

Skip the config file when running inside or against cloud credentials.

```
cloudlist -ad
```

Outcome: discovers usable providers (aws, gcp, azure, kubernetes) directly from the environment's credentials — convenient in CI pipelines or on a cloud instance with an attached role.

### Write a machine-readable inventory

Capture results for diffing over time.

```
cloudlist -json -o assets.json
```

Outcome: writes every discovered asset as JSON records to assets.json, suitable for storing and diffing against previous inventory runs to detect new or vanished assets.

*No corpus evidence — derived from upstream documentation.*
