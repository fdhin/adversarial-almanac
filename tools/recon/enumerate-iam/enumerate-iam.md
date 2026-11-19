---
name: "enumerate-iam"
categories:
  - blackarch-recon
version: "14.4529114"
homepage: "https://github.com/andresriancho/enumerate-iam"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "andresriancho/enumerate-iam"
github-stars: 1263
github-pushed: "2024-02-05"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# enumerate-iam

> Brute-forces which AWS API calls a credential set is allowed to make, using only non-destructive get*/list* operations.

## What it's for

enumerate-iam (andresriancho) answers the question "found AWS credentials — what can they actually do?" It takes an access key pair and systematically tries every AWS API call, logging the ones that succeed, so an unknown credential's effective permissions become visible. Only `get*` and `list*` calls are performed, making the enumeration non-destructive; if the credential permits `get_account_authorization_details`, the full IAM policy state is dumped in one shot. It was released as part of the Internet-Scale Analysis of AWS Cognito Security research. The brute-force test list lives in `enumerate_iam/bruteforce_tests.py` and can be regenerated from the aws-sdk-js API documentation when AWS ships new services.

## Typical use cases

### Enumerate permissions for a key pair

Discover what an unknown credential can do.

```
./enumerate-iam.py --access-key AKIA... --secret-key StF0q...
```

Outcome: logs the starting enumeration banner, dumps `get_account_authorization_details` output (roles, policies, users) when that call is allowed, then walks the API surface printing one `... worked!` line per permitted call (e.g. `sqs.list_queues() worked!`) — the permitted-call list is the credential's de facto permission set.

### Use as a library

Embed permission enumeration in another tool.

```
python3 -c "from enumerate_iam.main import enumerate_iam; print(enumerate_iam('<access_key>', '<secret_key>', '<session_token>', '<region>'))"
```

Outcome: imports the main entry point directly and returns all enumerated permission information as a Python dictionary instead of log lines, ready for programmatic processing.

No corpus evidence — derived from upstream documentation.
