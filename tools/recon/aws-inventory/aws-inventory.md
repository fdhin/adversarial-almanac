---
name: "aws-inventory"
categories:
  - blackarch-recon
version: "19.9a2fa8e"
homepage: "https://github.com/nccgroup/aws-inventory"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: AGPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/aws-inventory"
github-stars: 736
github-pushed: "2024-04-05"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aws-inventory

> Enumerate every resource that exists in an AWS account by walking all list/describe APIs in all regions.

## What it's for

NCC Group's aws-inventory answers "what is actually in this AWS account?". It uses botocore to discover which AWS services exist and which regions they run in, then invokes every list/describe-type API against each service in each region, printing the collected resources as JSON to stdout. That makes it useful for billing audits, for finding dormant or forgotten resources, and for security reviews where unknown resources are the finding. Expect it to take dozens of minutes unfiltered; an operation blacklist config file is used by default to skip the slowest operations. Run it with read-only credentials (the AWS managed `ViewOnlyAccess` policy is the reference permission set). On BlackArch the `aws-inventory` wrapper launches the Python 2 script `aws_inventory.py`; results can also be explored in the bundled React single-page app (open `gui/dist/index.html` and load the generated JSON).

## Typical use cases

### Inventory an entire account

```
aws-inventory
```

Outcome: discovers credentials via the standard chain (environment, instance metadata, config file, credentials file), walks all services across all regions, and prints a JSON document describing every resource found — pipe to `jq` or save it for the report.

### See which services and operations will be queried

```
aws-inventory --list-svcs
aws-inventory --list-operations
```

Outcome: the first command prints the AWS services known to botocore (acm, apigateway, autoscaling, …) purely from local service models; the second prints the list/describe operations per service plus the total operation count — no API calls are made, so use it to scope or sanity-check a run.

### Preview the API calls without running them

```
aws-inventory --debug --dry-run
```

Outcome: prints which APIs would be invoked for each service, letting you estimate the run or spot services you want excluded before spending time and API quota.

*No corpus evidence — derived from upstream documentation.*
