---
name: "easyda"
categories:
  - blackarch-automation
version: "7.0867f9b"
homepage: "https://github.com/nccgroup/easyda"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/easyda"
github-stars: 52
github-pushed: "2014-04-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# easyda

> Interactive pass-the-hash sprayer and Domain Admin finder built on Metasploit.

## What it's for

easyda (NCC Group's "Easy Windows Domain Access" script) automates two post-exploitation chores in a Windows domain: spraying a captured local-administrator hash or clear-text credentials across a host range via Metasploit's psexec-style modules to find every machine they unlock (pass the hash), and identifying which machines a Domain Administrator account is currently logged on to. It is guided rather than flag-driven: it asks what you hold (a single hash, a file of hashes, clear-text credentials, or a blank-password check), asks for the IP/CIDR range or a file of IPs, then drives msfconsole through the sweep and summarizes the findings. Requires Metasploit installed and root. The project is dormant (2014), so expect the module behavior of that era.

## Typical use cases

### Run the guided domain sweep

Feed it one credential and find every host it reaches.

```
sudo easyda
```

Outcome: after verifying Metasploit is present, prompts for the credential type (single LM:NTLM hash, hash list file, clear-text pair, or blank-password check) and the IP/CIDR range or IP file, then reports which hosts accept the credentials and where domain admin accounts are logged in.

*No corpus evidence — derived from upstream documentation.*
