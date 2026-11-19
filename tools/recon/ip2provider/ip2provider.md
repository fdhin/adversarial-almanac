---
name: "ip2provider"
categories:
  - recon
homepage: "https://github.com/oldrho/ip2provider"
origin: "cloudpentestcheatsheets"
recipe: "upstream"
packaged-by: []
build-system: python
license: GPL3
meta-checked: "2026-11-13"
github-repo: "oldrho/ip2provider"
github-match: "exact"
github-stars: 108
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# ip2provider

> Maps an IP address to the cloud provider — and often the service and region — that hosts it, using local copies of the providers' published IP ranges.

## What it's for

ip2provider answers a basic recon question: is this IP cloud-hosted, and by whom? It checks targets against locally stored route lists for AWS, Azure (public and government clouds), GCP, IBM/SoftLayer, Oracle Cloud, Alibaba Cloud, Linode, DigitalOcean, Rackspace and Cloudspace, reporting the owning provider and, for some providers, the service and region. It takes single IPs, comma-separated lists, or one-IP-per-line piped input, which makes it a natural post-processing step after port scanning or asset discovery: pipe resolved hostnames' IPs in and get a cloud-ownership map out. It is purely a lookup over published ranges — it says nothing about who operates the specific tenant behind an address.

## Typical use cases

### Look up a single address

Triage one target.

```
./ip2provider.py 198.51.100.10
```

Outcome: one result line per match naming the provider — and, where the provider publishes it, the service and region (e.g. an AWS region for an EC2 range); no output means the address is outside every tracked provider range.

### Classify a whole scan's worth of IPs

Bulk triage after discovery.

```
cat ip_addresses.txt | ./ip2provider.py
```

Outcome: reads one IP per line from stdin and prints a provider per line — feed it the output of DNS resolution or an asset list to split targets into cloud-hosted versus self-hosted; comma-separated IPs work as positional arguments too.

### Machine-readable output

Feed a report or script.

```
./ip2provider.py -o json 198.51.100.10
```

Outcome: a JSON array of matches instead of text lines; `-o raw` returns the unfiltered array including overlapping ranges for maximum information.

### Refresh the provider range lists

Ranges churn between engagements.

```
./ip2provider.py --update-lists
```

Outcome: re-downloads each provider's current published route lists before doing the lookup — run it before trusting results, since stale local lists silently miss new ranges.

No corpus evidence — derived from upstream documentation.
