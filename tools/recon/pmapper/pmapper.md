---
name: "pmapper"
categories:
  - blackarch-recon
version: "82.91d2e60"
homepage: "https://github.com/nccgroup/PMapper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: AGPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/PMapper"
github-stars: 1590
github-pushed: "2024-08-02"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pmapper

> Models an AWS account's IAM users and roles as a directed graph to expose privilege-escalation paths and who can really do what.

## What it's for

Principal Mapper (PMapper) audits AWS Identity and Access Management configuration. It ingests the users, roles and policies of an AWS account (or organization) and builds a directed graph of principals and their delegation relationships. On top of that graph it runs a local simulation of AWS's authorization logic: asking "who can do iam:CreateUser" also surfaces principals who could assume a role that can — indirect paths (an EC2 instance profile, a passrole chain) that a plain policy grep misses. It ships with preset queries for privilege escalation and can render the graph as an image. It only reads IAM configuration; it never performs actions against live resources. Needs valid AWS credentials (profile or environment) and graphviz for visualization.

## Typical use cases

### Build the IAM graph for an account

Collect the account's principals and policies into a local graph.

```
pmapper --profile prod graph create
```

Outcome: pulls IAM users, roles, and policies through botocore using the named AWS CLI profile, then builds and stores the graph; subsequent `query`, `argquery` and `visualize` calls reuse it without repeating the API walk.

### Ask who can perform an action

Natural-language query against the graph, indirect paths included.

```
pmapper --profile prod query 'who can do iam:CreateUser'
```

Outcome: lists every principal with direct permission plus those who can reach it via role-assumption or delegation chains — the difference between the two lists is usually the interesting finding.

### Query an action with resource and condition context

Use the argument form for a precise action/resource/condition triple.

```
pmapper --account 000000000000 argquery -s --action 'ec2:RunInstances' --condition 'ec2:InstanceType=c6gd.16xlarge'
```

Outcome: reports principals able to launch that specific instance type (skipping those already flagged as admins via `-s`); handy for "who can spend money" style checks.

### Run the privilege-escalation preset

Sweep the account for known escalation paths in one shot.

```
pmapper --account 000000000000 query -s 'preset privesc *'
```

Outcome: prints a report of all principals with known privilege-escalation routes and the edges that make them up, excluding principals the `-s` flag marks as already-admin.

### Visualize the graph

Render admin, privesc and inter-principal edges as an image.

```
pmapper --account 000000000000 visualize --filetype svg
```

Outcome: writes an SVG file of the principal graph suitable for report figures; `--only-privesc` restricts it to escalation edges.

No corpus evidence — derived from upstream documentation.
