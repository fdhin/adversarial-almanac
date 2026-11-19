---
name: "monkey365"
categories:
  - scanner
homepage: "https://github.com/silverhack/monkey365"
github-repo: "silverhack/monkey365"
github-match: "exact"
origin: "awesome-azure-pentest"
recipe: "upstream"
license: Apache-2.0
github-stars: 1372
github-pushed: "2026-10-23"
github-commits-year: 217
gh-checked: "2026-11-13"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# monkey365

> Self-contained PowerShell assessment framework for Microsoft 365, Entra ID and Azure — collects, rules-evaluates, and reports without needing any other Microsoft modules installed.

## What it's for

Monkey365 is a PowerShell-based security assessment framework for the Microsoft cloud stack. It collects configuration data across Microsoft 365, Entra ID and Azure, evaluates findings against built-in security rules, and produces reports with evidence and remediation guidance per finding. The module is deliberately self-contained — its runtime dependencies are bundled, so it does not require the Azure CLI, Az PowerShell, ExchangeOnlineManagement, or the Graph SDK, which makes it a drop-in collector on an assessment jump box. With a foothold credential it doubles as offensive reconnaissance: one run enumerates the tenant's misconfigurations worth abusing.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run a Microsoft 365 assessment including Entra ID

```
$options = @{
    Instance       = 'Microsoft365'
    Collect        = @('ExchangeOnline','MicrosoftTeams','SharePointOnline')
    PromptBehavior = 'SelectAccount'
    IncludeEntraID = $true
    ExportTo       = 'HTML'
}
Invoke-Monkey365 @options
```

Outcome: Prompts for the account to use, collects the selected workloads plus Entra ID, and writes an HTML report of every rule failure with evidence — the services reachable depend on the signed-in identity's permissions.

### Install from the PowerShell Gallery

```
Install-Module -Name monkey365 -Scope CurrentUser
Import-Module monkey365
```

Outcome: Installs the self-contained module user-scoped; no Az/Graph/Exchange modules are needed alongside it.

