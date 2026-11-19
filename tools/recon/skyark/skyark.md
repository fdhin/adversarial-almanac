---
name: "skyark"
categories:
  - recon
homepage: "https://github.com/cyberark/SkyArk"
github-repo: "cyberark/SkyArk"
github-match: "exact"
origin: "awesome-azure-pentest"
recipe: "upstream"
license: MIT
github-stars: 913
github-pushed: "2024-12-17"
gh-checked: "2026-11-13"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# skyark

> Discovers the most privileged entities in Azure and AWS — including the 'shadow admins' whose non-obvious permissions still escalate to full control.

## What it's for

SkyArk is CyberArk's cloud shadow-admin scanner with two modules: AzureStealth (Azure/Entra) and AWStealth (AWS). Both enumerate the users, groups and roles holding the most sensitive permissions — not just the named administrators but entities whose seemingly limited rights chain to full admin (the shadow-admin class from CyberArk's RSA 2018/2020 research). From an assessment credential, the output is the prioritized target list: which identities are worth stealing, and which stealthy-privileged accounts the defenders likely are not watching. Age shows (2018-era lineage, sparse commits) but the permission-analysis approach still maps to modern privilege-escalation paths.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Load and run the Azure scan

```
Import-Module .\SkyArk.ps1 -force
Invoke-AzureStealth -Username admin@targetdomain.com -Password *** -Tenant company.onmicrosoft.com
```

Outcome: Authenticates with assessment credentials and enumerates the Azure/Entra privileged-entity set — straight admins plus shadow admins — as an HTML report for target selection.

### Run the AWS shadow-admin scan

```
Import-Module .\SkyArk.ps1 -force
Invoke-AWStealth
```

Outcome: Enumerates IAM users, groups and roles with sensitive-permission chains in the credential-active AWS account; the output ranks which stolen identities convert to full control.

