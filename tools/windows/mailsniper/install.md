---
name: "mailsniper-install"
categories:
  - windows
---

# Installing mailsniper

Single PowerShell script — download and dot-source it in a PowerShell session on a host that can reach the Exchange/OWA endpoints:

```powershell
git clone https://github.com/dafthack/MailSniper.git
cd MailSniper
. .\MailSniper.ps1
```

The functions (`Invoke-SelfSearch`, `Invoke-GlobalMailSearch`, `Get-GlobalAddressList`, `Invoke-PasswordSprayOWA`, ...) are then available in the session.

Notes: runs under Windows PowerShell on Windows; some modules (SelfSearch with `-UsePrt`/`-AccessToken`) target newer O365 auth flows. Loading a known offensive script into memory on a monitored host will trip AV/EDR — stage it accordingly.

Verification: `Get-Command Invoke-SelfSearch` after dot-sourcing.
