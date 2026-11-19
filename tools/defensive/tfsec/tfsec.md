---
name: "tfsec"
categories:
  - blackarch-defensive
version: "v0.63.1.r408.gb692c20b6"
homepage: "https://github.com/aquasecurity/tfsec"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-05-03"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "aquasecurity/tfsec"
github-stars: 7059
github-pushed: "2026-03-25"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tfsec

> Static-analysis scanner that flags security misconfigurations in Terraform code across cloud providers — now consolidated into Trivy.

## What it's for

tfsec parses Terraform HCL (and Terraform CDK output) and evaluates it against hundreds of built-in checks for AWS, Azure, GCP and other providers — open security groups, unencrypted storage, exposed secrets in variables, missing logging — before anything is deployed. It resolves local and remote modules, evaluates HCL expressions and Terraform functions, and reasons about relationships between resources, so findings reflect the effective configuration rather than raw text. Aqua has merged tfsec's engine into Trivy: new adoption should start with Trivy's Terraform/IaC scanning, while tfsec remains available and functional. The exit status is non-zero when problems are found, which makes it a natural CI gate.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a Terraform project

Check the current tree for misconfigurations.

```
tfsec .
```

Outcome: a formatted list of findings — rule ID, severity, file and line, and the offending code — followed by a summary; the exit code is 0 when clean and non-zero when problems are found, so the command works directly as a CI pass/fail gate. Without a directory argument the current working directory is scanned.

### Emit machine-readable results

Feed findings into CI tooling or a dashboard.

```
tfsec --format json .
```

Outcome: the same scan printed as a JSON document (one entry per finding) instead of the default human-readable format; `--format` also accepts `sarif`, `csv`, `checkstyle`, `junit` and `text` for direct integration with code-scanning dashboards.

### Include variable values from a tfvars file

Scan the configuration as it would actually be deployed.

```
tfsec --tfvars-file terraform.tfvars .
```

Outcome: variable references resolve to the concrete values from the tfvars file, so checks that depend on assigned values (e.g. a CIDR range or a flag) evaluate the real deployment inputs instead of falling back to defaults.

### Disable specific checks

Skip rules that are accepted for this project.

```
tfsec . -e general-secrets-sensitive-in-variable,google-compute-disk-encryption-customer-keys
```

Outcome: the listed check IDs are excluded from the run and the report shows only remaining findings. For silencing a single offending line instead of a whole check, put `#tfsec:ignore:<RULE_ID>` on the line — optionally with an expiry to force re-review, e.g. `#tfsec:ignore:aws-s3-enable-bucket-encryption:exp:2027-01-02`, which deactivates after that date.
