---
name: "backdoorme"
categories:
  - blackarch-backdoor
version: "308.f9755ca"
homepage: "https://github.com/Kkevsterrr/backdoorme"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Kkevsterrr/backdoorme"
github-stars: 750
github-pushed: "2017-12-20"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backdoorme

> Metasploit-style console that plants and schedules persistence backdoors on Unix hosts you already reach over SSH.

## What it's for

Backdoorme automates the post-exploitation phase after you hold credentials or a session on a Unix target: it deploys small listeners ("backdoors" written in bash, sh, perl, python, php, ruby, java, netcat, or Metasploit payloads) and layers "modules" on top that make them persistent — cron scheduling, startup files, command poisoning, whitelist restrictions. It is not an exploitation tool: it assumes you already have SSH access (ideally root, which its escalation backdoors require to plant) and keeps that access alive. Targets are Unix-like hosts; a `windows` backdoor using msfvenom also exists.

## Typical use cases

### Start the console and register a target

Add the compromised host you will be planting backdoors on.

```
python master.py
>> addtarget
Target Hostname: target.example.com
Username: user
Password: ********
```

Outcome: the host is registered as "Target 1" and becomes the current target; further backdoors deploy over this SSH connection. `use <backdoor> 2` retargets a later backdoor at target 2, and `list` shows every target with its connection state.

### Deploy a backdoor

Plant a listener on the current target.

```
>> use shell/metasploit
(msf) >> show options
(msf) >> set name apache
(msf) >> run
```

Outcome: Backdoorme builds the chosen backdoor (here an msfvenom reverse-TCP binary named `apache`), uploads and starts it on the target; a handler on your listener address catches the callback. `show options`/`set` work per backdoor, Metasploit-style.

### Layer a persistence module

Make an already-selected backdoor fire repeatedly.

```
(msf) >> add cron
```

Outcome: the selected backdoor is added to the root user's crontab on the target and re-executes on the configured schedule. Other modules include `poison` (drops a trojaned `ls`-style command that relaunches the backdoor whenever the user runs it), `startup` (bashrc/init hooks) and `whitelist` (restrict callbacks to one IP).

*No corpus evidence — derived from upstream documentation.*
