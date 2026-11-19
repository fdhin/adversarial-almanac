---
name: "mfasweep"
categories:
  - recon
homepage: "https://github.com/dafthack/MFASweep"
github-repo: "dafthack/MFASweep"
github-match: "exact"
origin: "awesome-azure-pentest"
recipe: "upstream"
license: BSD-3-Clause
github-stars: 1716
github-pushed: "2026-04-13"
github-commits-year: 18
gh-checked: "2026-11-13"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# mfasweep

> Probes eleven Microsoft authentication endpoints with one credential set to find the protocol conditional access forgot — the MFA gap.

## What it's for

MFASweep is a PowerShell script that attempts authentication against a spread of Microsoft services (Graph API, Azure Resource Manager, Exchange Web Services, the M365 web portal with seven user-agent/device personas including a Nintendo Switch UA, ActiveSync, and optionally an on-prem ADFS server) and reports which succeed — i.e., which flows are effectively single-factor despite tenant MFA policy. It is the canonical MFA-inconsistency probe for a harvested credential. WARNING the tool itself carries: each run performs eleven-plus logins (twelve with ADFS), which can lock out an account with a wrong password. Conditional-access blocks (AADSTS53003) are detected and reported separately from generic auth failures.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Sweep all default services and capture tokens

```
Invoke-MFASweep -Username targetuser@targetdomain.com -Password *** -WriteTokens
```

Outcome: Eleven authentication attempts across Graph, ARM, EWS, the web portal (7 user agents) and ActiveSync; successful flows are listed and their tokens/cookies written to AccessTokens.json — every success is an MFA-bypass lane.

### Sweep plus ADFS detection and on-prem login

```
Invoke-MFASweep -Username targetuser@targetdomain.com -Password *** -Recon -IncludeADFS
```

Outcome: Adds ADFS realm discovery from the domain and, when detected, a twelfth login against the on-prem ADFS server — on-prem federation endpoints frequently sit outside cloud conditional access.

### Probe a single service module

```
Invoke-GraphAPIAuth -Username targetuser@targetdomain.com -Password ***
```

Outcome: Runs one module standalone (Graph here; the per-service commands are all exported) for quiet, targeted checks that avoid the full sweep's login volume.

