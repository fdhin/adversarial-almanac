---
name: "cloud-audit"
categories:
  - blackarch-scanner
version: "2.5.0.r0.g0207aa0"
homepage: "https://github.com/gebalamariusz/cloud-audit"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-15"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "gebalamariusz/cloud-audit"
github-stars: 74
github-pushed: "2026-10-14"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 143
gh-activity-checked: "2026-11-09"
---

# cloud-audit

> Read-only AWS security scanner with privilege-escalation paths, blast-radius analysis and a Terraform/AWS CLI fix on every finding.

## What it's for

cloud-audit (gebalamariusz) is an open-source, strictly read-only AWS account scanner: 110 checks across ~25 services, 64 IAM privilege-escalation methods and 31 attack-chain rules, with every finding carrying an AWS CLI and Terraform remediation. Beyond flat findings it computes the blast radius of any resource, can prove escalation paths with the IAM policy simulator (`--verify`) instead of guessing, and has an `agent-blast` mode mapping what a hijacked or prompt-injected Bedrock Agents / AgentCore identity could reach. Nothing is written to the account, and the AWS-managed SecurityAudit policy is sufficient to run it. A demo mode runs the same engines against a built-in sample account for offline exploration. It covers AWS only — no other cloud provider.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Full account scan

Assess the current AWS profile and region.

```
cloud-audit scan
```

Outcome: collects account metadata read-only and prints findings grouped by check, each with severity, affected resources and a ready-to-apply AWS CLI + Terraform fix.

### Prove escalation paths

Add simulator-backed evidence to the findings.

```
cloud-audit scan --verify
```

Outcome: in addition to the static checks, candidate IAM privilege-escalation paths are run through the IAM policy simulator, so each path is reported as verified or unverified rather than inferred.

### Explore on the sample account offline

Exercise the engines with no AWS account at all.

```
cloud-audit demo --save demo.json
```

Outcome: runs a full scan of the built-in invented sample account and saves it to demo.json; follow-up commands such as `blast-radius --report demo.json --resource <ARN>`, `simulate --report demo.json --fix <check-id>` and `exposure --report demo.json` then explore attack chains, single findings and exposure offline.

### Assess what a hijacked AI agent can reach

Analyze agent blast radius.

```
cloud-audit agent-blast --verify
```

Outcome: a per-agent report under two threat models — identity takeover (stolen role credentials) and behaviour takeover (indirect prompt injection) — listing the agent's tool/execution roles, their reach and any simulator-confirmed escalation path to account admin.
