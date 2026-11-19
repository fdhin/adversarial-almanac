---
name: "scubagear"
categories:
  - defensive
homepage: "https://github.com/cisagov/ScubaGear"
github-repo: "cisagov/ScubaGear"
github-match: "exact"
origin: "awesome-azure-pentest"
recipe: "upstream"
license: MIT
github-stars: 2799
github-pushed: "2026-11-14"
github-commits-year: 200
gh-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# scubagear

> CISA's own M365 tenant baseline assessor — the reference tool for measuring a Microsoft 365 tenant against the SCuBA secure-configuration baselines.

## What it's for

ScubaGear (CISA's Secure Cloud Business Applications project) queries Microsoft 365 APIs for tenant configuration, evaluates the collected settings against Rego policies implementing the published SCuBA baselines (Entra ID, Exchange Online, Defender, Teams, Power BI, Power Platform, SharePoint, OneDrive), and renders HTML/JSON/CSV reports with per-control pass/fail and NIST 800-53 / MITRE ATT&CK mappings. For an attacker-held tenant credential it is a one-command configuration disclosure: it enumerates the security posture (conditional-access state, legacy-auth, MFA coverage, external sharing) that tells you which misconfigurations to abuse. For defenders it is the baseline reference the offensive tooling is measured against. PowerShell 5+ on Windows; PSGallery distribution.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Assess every M365 product against the SCuBA baselines

```
Invoke-SCuBA -ProductNames *
```

Outcome: Full-tenant assessment run: interactive login, per-product policy evaluation via Open Policy Agent, and an HTML+JSON+CSV report under the output directory naming every control that fails.

### Assess only Entra ID and Exchange Online

```
Invoke-SCuBA -ProductNames aad,exo
```

Outcome: Scoped run for the two products that matter most on identity takeovers — conditional access, legacy authentication, and mailbox security controls only.

### Install dependencies after the module

```
Install-ScubaDependencies
```

Outcome: Pulls the required PowerShell modules and the Open Policy Agent binary the policy engine shells out to; run once after Install-Module ScubaGear.

### Confirm which account and tenant a run used

```
Get-ScubaGearContext
```

Outcome: Prints the account/tenant of the last run — ScubaGear deliberately does not print them to console, so this is the audit trail when multiple credentials are in play.

