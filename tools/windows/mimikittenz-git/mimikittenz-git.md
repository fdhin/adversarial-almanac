---
name: "mimikittenz-git"
categories:
  - windows
version: "20160709.r8"
homepage: "https://github.com/orlyjamie/mimikittenz"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "orlyjamie/mimikittenz"
github-stars: 1868
github-pushed: "2024-06-28"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mimikittenz-git

> Post-exploitation PowerShell script that scrapes plaintext credentials and other secrets out of target process memory with ReadProcessMemory() and regex patterns.

## What it's for

mimikittenz (original author putterpanda, GitHub account since renamed to orlyjamie) is a post-exploitation PowerShell tool for user-level (non-admin) sensitive-data extraction. It opens running processes and scans their address space with the Windows `ReadProcessMemory()` API against a bank of regex patterns that match login form data in memory — plaintext passwords for webmail (Gmail, Office365, Outlook Web), accounting apps (Xero, MYOB), remote access portals (Juniper SSL-VPN, Citrix NetScaler, RD Web Access), developer/SaaS services (Jira, GitHub, Bugzilla, Zendesk), Dropbox, OneDrive, AWS and more.

Because it reads the memory of live processes, secrets must still be resident: once a process exits, its memory is gone. It needs no admin rights for same-user processes and leaves no credentials on disk beyond the console output, making it a cheap value-add sweep after landing on a user workstation. Custom patterns for any other web application are a one-line addition.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run the built-in credential sweep

```
powershell -ExecutionPolicy Bypass -Command "Import-Module .\Invoke-mimikittenz.ps1; Invoke-mimikittenz"
```

Outcome: imports the script (entry function `Invoke-mimikittenz`, defined in `Invoke-mimikittenz.ps1`) and scans the built-in target processes with the bundled regex bank; every match prints as the pattern name plus the captured credential string (for example a Gmail or Outlook Web password) pulled from live process memory.

### Teach it a new web application

```
[mimikittenz.MemProcInspector]::AddRegex("ExampleApp", "user=.{1,48}&password=.{1,32}&login=")
Invoke-mimikittenz
```

Outcome: registers a custom named regex against the `MemProcInspector` .NET type so the next scan also harvests credentials matching that application's login form encoding; the same mechanism extracts other juicy data such as TRACK2 card data or PII from merchant/POS processes.

### Limit the scan to chosen processes

```
[mimikittenz.MemProcInspector]::InspectManyProcs("iexplore", "chrome", "firefox")
```

Outcome: restricts inspection to the named processes instead of the script's full target list, which speeds up the sweep and reduces noise when only specific browsers or client apps are of interest.
