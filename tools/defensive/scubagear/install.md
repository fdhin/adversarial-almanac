---
name: "scubagear-install"
categories:
  - defensive
---

# Installing scubagear

PSGallery is the distribution channel:

```powershell
Install-Module -Name ScubaGear
Install-ScubaDependencies
```

PowerShell 5 on Windows; the dependency installer also fetches Open Policy Agent. Verification: `Invoke-SCuBA -Version`.