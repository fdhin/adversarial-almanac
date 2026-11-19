---
name: "checkov"
categories:
  - blackarch-code-audit
version: "3.3.30.r1.g811d068790"
homepage: "https://github.com/bridgecrewio/checkov"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "bridgecrewio/checkov"
github-stars: 9139
github-pushed: "2026-11-02"
github-archived: false
github-issues: 222
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 230
gh-activity-checked: "2026-11-09"
---

# checkov

> Static scanner for infrastructure-as-code and container images against 1000+ built-in cloud-security policies.

## What it's for

Checkov statically scans infrastructure-as-code — Terraform (including rendered plan files), CloudFormation, Kubernetes, Helm, Kustomize, Dockerfile, Serverless, Ansible, Bicep, ARM, OpenTofu, and CI pipeline definitions — against a large built-in library of security and compliance checks (CKV_* policy IDs covering AWS, Azure, and GCloud best practices), plus software-composition analysis of packages and container images. Reach for it before `terraform apply`: it parses templates without touching the cloud and reports misconfigurations (unencrypted storage, open security groups, permissive IAM) with the policy ID, offending resource, file:line, and a remediation guide link. Template scanning needs no credentials; platform-linked features such as severity thresholds and SCA require an API key.

## Typical use cases

### Scan a whole IaC repository

```
checkov --directory .
```

Outcome: every supported file under the current directory is scanned; the console report prints a summary line ("Passed Checks: N, Failed Checks: M, Suppressed Checks: K") followed by per-check results with policy ID (e.g. CKV_AWS_21), resource name, file:line, and a remediation guide URL.

### Scan selected files only

```
checkov -f main.tf -f vpc.yaml
```

Outcome: the same report limited to the named files — a quick look at one module or stack without running the repository-wide scan.

### Scan a rendered Terraform plan

```
terraform plan -out tf.plan
terraform show -json tf.plan | jq '.' > tf.json
checkov -f tf.json
```

Outcome: evaluates what the plan would actually create, after variable evaluation, rather than just template text; findings reference the planned resource values (note that single-line plan JSON reports line 0).

### Run only — or all but — chosen checks

```
checkov -d . --check CKV_AWS_20,CKV_AWS_57
checkov -d . --skip-check CKV_AWS*
```

Outcome: the first runs only the two named policies, the second runs everything except IDs matching the glob — the standard way to phase new checks into a codebase gradually.

### Emit a SARIF report for CI

```
checkov -d . -o sarif
```

Outcome: writes `results.sarif` in the working directory, suitable for GitHub code scanning and other SARIF-consuming viewers; the `--output` flag selects the format (default `cli`).

*No corpus evidence — derived from upstream documentation.*
