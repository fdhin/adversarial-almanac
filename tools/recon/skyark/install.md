---
name: "skyark-install"
categories:
  - recon
---

# Installing skyark

PowerShell project cloned whole:

```powershell
git clone https://github.com/cyberark/SkyArk
Import-Module .\SkyArk\SkyArk.ps1 -force
```

AzureStealth requires the Azure module present (it installs/imports as needed); AWStealth requires AWS credentials in the environment.