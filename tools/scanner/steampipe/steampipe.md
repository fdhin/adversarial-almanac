---
name: "steampipe"
categories:
  - scanner
homepage: "https://github.com/turbot/steampipe"
origin: "cloudpentestcheatsheets"
recipe: "upstream"
packaged-by: []
build-system: go
license: AGPL3
meta-checked: "2026-11-13"
github-repo: "turbot/steampipe"
github-match: "exact"
github-stars: 8006
gh-checked: "2026-11-13"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# steampipe

> Query live cloud and SaaS APIs as Postgres tables with plain SQL — `select * from cloud;`, zero ETL.

## What it's for

Steampipe (Turbot) exposes APIs and services as database tables: a single Go binary bundles a Postgres-compatible database, and plugins map each service's REST API to queryable tables in real time — over 2000 tables across AWS, Azure, GCP, Kubernetes, GitHub, Microsoft 365 and hundreds more from the plugin hub. It is dual-use by nature. Defensive teams point it at their own accounts for inventory and compliance benchmarks; offensive teams use it during cloud assessments to enumerate and cross-join account data with SQL instead of pages of CLI describe-calls — the CloudFox project itself prototyped many of its checks with Steampipe. Queries run live against the API on each execution (no data warehouse), can fan out across many connections in parallel, and the same plugin tables are also available as Postgres FDWs, SQLite extensions and standalone export binaries. The core is AGPL-3.0 and free; the hosted Turbot Pipes workspace is a commercial add-on.

## Typical use cases

### Install a plugin for the target cloud

Set up the tables before querying.

```
steampipe plugin install aws
```

Outcome: downloads the AWS plugin and registers its tables in the local database; credentials come from the provider's standard chain (for AWS: environment variables, `~/.aws/credentials` or an instance role), so no separate login is needed.

### Enumerate an account with one SQL statement

Replace a screenful of CLI loops.

```
steampipe query "select instance_id, instance_state, public_ip_address from aws_ec2_instance where public_ip_address is not null"
```

Outcome: every internet-exposed EC2 instance across all regions in one table — filter, join and aggregate across any of the plugin's tables (`aws_s3_bucket`, `aws_iam_user`, ...) exactly as in a database, with the API called live behind the query.

### Explore interactively

Ad-hoc analysis with table discovery.

```
steampipe query
```

Outcome: opens an interactive SQL shell against the bundled Postgres — type `.inspect aws` to list every table the plugin exposes with descriptions and copy/paste example queries, then select from anything you find.

### Check your own exposure from the same seat

The defensive half of the dual use.

```
steampipe query "select name, bucket_policy_is_public from aws_s3_bucket where bucket_policy_is_public"
```

Outcome: rows for every publicly-policy'd S3 bucket in the connected accounts — the same enumeration an assessor would run, usable continuously as a drift check; the wider Steampipe ecosystem (mods such as compliance benchmarks via `steampipe check`) builds on the same tables.

No corpus evidence — derived from upstream documentation.
