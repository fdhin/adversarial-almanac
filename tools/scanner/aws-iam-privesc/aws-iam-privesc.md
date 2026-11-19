---
name: "aws-iam-privesc"
categories:
  - blackarch-scanner
version: "11.2983efd"
homepage: "https://github.com/cyberqueenmeg/aws_iam_privesc"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cyberqueenmeg/aws_iam_privesc"
github-stars: 3
github-pushed: "2023-07-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aws-iam-privesc

> Python scanner that pulls the IAM policies behind a set of AWS keys and reports every privilege-escalation path available to each user, split into confirmed and potential methods.

*No corpus evidence — derived from upstream documentation (README plus the packaged `aws_escalate.py` source).*

## What it's for

Answering "what can these AWS credentials escalate to?" without manually reading policy JSON. The tool collects the inline and attached user and group policies affecting each IAM user, evaluates them against a catalogue of known escalation techniques (permission sets such as `iam:CreateAccessKey` or `iam:PutRolePolicy` on the right resources), and prints a per-user verdict: `Already an admin!`, or the methods that are confirmed (all needed permissions allowed and not denied) versus merely potential (wildcard-scoped or partially denied). It was written by Megan G. Howell (cyberqueenmeg) as an updated, BlackArch-packaged extension of Rhino Security Labs' deprecated `aws_escalate.py` — the lineage behind the privilege-escalation-methods research. It audits existing users; it does not attempt the escalations themselves. Keys are passed as flags or entered at the interactive prompt when omitted.

## Typical use cases

### Audit the identity that owns the keys

```
aws-iam-privesc --access-key-id <ACCESS_KEY_ID> --secret-key <SECRET_KEY>
```

Outcome: collects the policies affecting the key owner and prints the user's escalation report — confirmed methods, potential methods, or "Already an admin!". Omit the key flags and the tool prompts for them interactively instead.

### Audit one named user

```
aws-iam-privesc --user-name user --access-key-id <ACCESS_KEY_ID> --secret-key <SECRET_KEY>
```

Outcome: the same analysis scoped to a single named account (a service or break-glass user) rather than the key owner — useful when you hold admin keys and are reviewing a specific principal.

### Audit every user in the account

```
aws-iam-privesc --all-users --access-key-id <ACCESS_KEY_ID> --secret-key <SECRET_KEY>
```

Outcome: iterates all IAM users ("Collecting policies for N users..."), reporting each one's escalation paths — the account-wide privilege-escalation review. Add `--session-token` when authenticating with temporary credentials.
