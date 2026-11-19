---
name: "shard"
categories:
  - blackarch-recon
version: "1.5"
homepage: "https://github.com/philwantsfish/shard"
packaged-by: [blackarch, aur]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "philwantsfish/shard"
github-stars: 406
github-pushed: "2016-10-05"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shard

> Check whether one credential opens many doors — shared-password detection across popular sites' login flows.

## What it's for

Shard takes a username/password pair (or a whole credential dump) and attempts authentication against a catalog of sites — Facebook, LinkedIn, Reddit, Twitter, Instagram, GitHub, BitBucket, DigitalOcean, Vimeo and others — reporting which services accept the pair. Its purpose is quantifying password reuse once you already hold credentials, not cracking them. It is a JVM tool shipped as a fat jar and run with `java -jar shard.jar`; available site modules are listed with -l, filtered with --modules, and new ones are plain Scala classes.

## Typical use cases

### Test a single credential

```
java -jar shard.jar -u user@example.com -p 'Password1'
```

Outcome: prints the credential alongside every site that accepted it (e.g. "user@example.com:Password1 - Reddit, Instagram") — any hit means the password is reused beyond its origin.

### Test a credential dump

```
java -jar shard.jar -f creds.txt
```

Outcome: parses one credential per line in the default `"username":"password"` format and reports accepted sites per line; dumps in a different shape work via --format with a regex whose two capture groups are username and password.

### Scope a run to specific sites

```
java -jar shard.jar -f creds.txt --modules GitHub,BitBucket,DigitalOcean
```

Outcome: only the named modules run, keeping large files fast and quiet instead of hammering every social site.

### See which site modules are available

```
java -jar shard.jar -l
```

Outcome: prints the full module list so targets can be picked deliberately before a run.

*No corpus evidence — derived from upstream documentation.*
