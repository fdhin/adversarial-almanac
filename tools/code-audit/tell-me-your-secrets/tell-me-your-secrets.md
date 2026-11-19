---
name: "tell-me-your-secrets"
categories:
  - blackarch-code-audit
version: "v2.4.2.r3.g5434b9d"
homepage: "https://github.com/valayDave/tell-me-your-secrets"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "valayDave/tell-me-your-secrets"
github-stars: 49
github-pushed: "2024-08-16"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tell-me-your-secrets

> Filesystem sweep for leaked credentials: walks a directory tree and flags secret-bearing files and content — private keys, cloud API keys, database configs, password stores — against 120+ built-in signatures.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Auditing a machine or project tree for secrets left behind: freshly provisioned cloud hosts, developer workstations, source trees about to be shipped. It walks from a root path and matches four aspects of each file — filename, extension, path and contents — against a signature list (regex or literal) derived from the shhgit signature set: AWS/Stripe/Slack/MailGun keys, SSH private keys, `.aws/credentials`, `id_rsa`, shadow files, keystore and wallet databases, shell histories, and more. Scans run in parallel across CPU cores. It only matches patterns — it never contacts a service to check whether a found key still works, so treat hits as leads to verify and rotate.

## Typical use cases

### Sweep a whole tree for any known secret type

```
tell-me-your-secrets /home/user
```

Outcome: every non-skipped file under the root is checked against all signatures; each match is logged with the file path and the signature that fired, giving a quick inventory of what is lying around on a host.

### Restrict the scan to a few signature families

```
tell-me-your-secrets /srv/app -f aws ssh google
```

Outcome: only signatures whose names contain one of the `-f` keywords are loaded (here: AWS keys and private keys, Google/OAuth material), cutting noise when you already know what class of leak you are hunting; `-f` accepts any space-separated keyword list.

### Write findings to CSV and fail on hits

```
tell-me-your-secrets /srv/app -w findings.csv -e
```

Outcome: the full match list is written as CSV rows to `findings.csv` (`.csv` is appended if the name lacks it), and the process exits non-zero when at least one secret was found — the shape a CI job or cron audit can gate on.

### Scan a repo with house signatures and gitignore awareness

```
tell-me-your-secrets /srv/app -g -c custom-config.yml
```

Outcome: files matched by the target's own `.gitignore` are skipped, and signatures come from `custom-config.yml` instead of the built-in list — same schema (blacklists plus `part`/`match|regex`/`name` entries), so you can encode company-internal key formats or suppress noisy rules.
