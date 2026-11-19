---
name: "weirdaal"
categories:
  - blackarch-webapp
version: "331.c14e36d"
homepage: "https://github.com/carnal0wnage/weirdAAL"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "carnal0wnage/weirdAAL"
github-stars: 844
github-pushed: "2025-01-13"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# weirdaal

> Python library and runner answering "what can this AWS keypair actually do?", plus offensive functions per service.

## What it's for

WeirdAAL (AWS Attack Library, by carnal0wnage) answers what a holder of an AWS keypair can do with it, in black-box fashion. It wraps boto3 with a module per AWS service (IAM, S3, EC2, Lambda, CloudTrail, DynamoDB, CloudFront, and many more) providing both enumeration and offensive/abuse functions. Credentials go into a `.env` file in the installed directory (standard AWS keypair format; STS tokens supported via boto3), and targets are tracked by name with `-t`. Run the recon module first to learn what the key allows, then invoke per-service modules. It assumes valid keys and permission to test them; it is a library of functions, not an exploit for AWS itself.

## Typical use cases

### Configure the keypair under test

Copy the sample credentials file and edit it.

```
cp env.sample .env
```

Outcome: creates `.env`, read at startup by overriding `AWS_SHARED_CREDENTIALS_FILE`; put the `aws_access_key_id` / `aws_secret_access_key` under test inside — invalid keys make every run exit with "The AWS Access Keys are not valid/active".

### Run full reconnaissance with the key

```
weirdaal -m recon_all -t MyTarget
```

Outcome: attempts actions across every service the boto3 library covers, printing the account ID, whether the key is root, and per-service lists of allowed actions (with access-denied errors for the rest) — the map for choosing follow-up modules.

### List what the key is authorized to call

```
weirdaal -m list_services_by_key -t MyTarget
```

Outcome: prints a condensed `service:action` enumeration of everything the key may call (e.g. `ec2:DescribeInstances`, `cloudwatch:ListMetrics`).

### Invoke a single service module

```
weirdaal -m iam_get_account_summary -t MyTarget
```

Outcome: runs that one IAM module against the configured keypair (here the account summary with password, MFA, and access-key quotas); any module from the per-service module sets can be substituted (e.g. `iam_list_groups`, `iam_check_root_account`).

*No corpus evidence — derived from upstream documentation (project wiki: Setup and Usage pages).*
