---
name: "metasploit-autopwn"
categories:
  - blackarch-automation
version: "12.09320cc"
homepage: "https://github.com/hahwul/metasploit-autopwn"
packaged-by: [blackarch]
blackarch-builddate: "2019-12-24"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hahwul/metasploit-autopwn"
github-stars: 285
github-pushed: "2019-12-23"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# metasploit-autopwn

> msfconsole plugin that restores the long-removed `db_autopwn` mass-exploitation command.

## What it's for

Rapid7 dropped `db_autopwn` from Metasploit years ago; this plugin (`db_autopwn.rb`) re-adds it. `db_autopwn` fires every exploit module whose target port/service matches the hosts recorded in the current Metasploit workspace database, so the workflow is: get hosts and services into the DB (typically with `db_nmap`), then let the plugin iterate matches automatically. It is deliberately brute and noisy — thousands of exploit attempts against one host — and is mainly of historical or lab-demonstration value today; the plugin itself prints a DEPRECATED notice on every run.

## Typical use cases

### Load the plugin into msfconsole

Enable the `db_autopwn` command for the session.

```
msfconsole -q
load db_autopwn
```

Outcome: msfconsole starts, loads the plugin from the framework plugins directory, and confirms `[*] Successfully loaded plugin: db_autopwn`; the `db_autopwn` command is now available. (The plugin file must already be installed in Metasploit's plugins directory — see install.md.)

### Mass-exploit services recorded in the workspace

Populate the workspace database, then auto-attack what it contains.

```
db_nmap -sV 198.51.100.0/24
db_autopwn -p -R great -e -q 198.51.100.10
```

Outcome: `db_nmap` stores discovered hosts/services in the workspace; `db_autopwn` matches exploits against them — `-p` matches by port, `-R great` restricts to modules ranked great or better, `-e` actually launches them, `-q` quiets per-module banners — printing progress lines like `(3/1301 [0 sessions]): Launching exploit/linux/http/... against 198.51.100.10:80...`. Any successful exploitation opens a Meterpreter/shell session; the counter in brackets tracks how many have landed. Expect crashing services and a very loud event log.

*No corpus evidence — derived from upstream documentation.*
