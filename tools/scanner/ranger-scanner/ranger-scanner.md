---
name: "ranger-scanner"
categories:
  - blackarch-scanner
version: "149.3aae5dd"
homepage: "https://github.com/funkandwagnalls/ranger"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "funkandwagnalls/ranger"
github-stars: 431
github-pushed: "2017-11-17"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ranger-scanner

> ranger — a Python 2 attack console for Windows networks: given credentials or NTLM hashes, it drives Impacket-style execution methods (wmiexec/smbexec/psexec/atexec) to run commands, inject PowerShell in memory, and dump domain data.

## What it's for

ranger (funkandwagnalls) wraps Impacket execution methods, PowerView-style domain queries and PowerShell memory injection (Mimikatz and friends) behind one CLI. Give it a username plus a password or hash (plain, `LM:NTLM`, `:NTLM` and PWDUMP forms are all accepted) and a domain, and it will scout logged-in users, open semi-interactive shells, execute one-off commands, inject PowerShell payloads without touching disk, or enumerate domain and forest structure — across single hosts, CIDRs, ranges, comma-separated target lists, or nmap XML output, with matching exclusion options. It also generates "pasteable" one-liner attacks for when your only channel is a clipboard. It assumes you already have valid credentials or hashes: it is post-access tooling, not initial access.

## Typical use cases

### Scout logged-in users across the domain

See where sessions are before picking execution targets.

```
ranger.py -u user -p 'Password1' -d CORP --scout
```

Outcome: authenticates across the domain scope and reports logged-in users — the reconnaissance step that feeds target selection for the execution methods.

### Open a semi-interactive shell on a host

Get an Impacket-style shell with one credential set.

```
ranger.py -u user -p 'Password1' -d CORP -t 198.51.100.10 --smbexec -v
```

Outcome: lands a semi-interactive shell on the target; swap `--smbexec` for `--wmiexec`, `--psexec` or `--atexec` to change the delivery method, and repeat `-v` (as `-vv`, `-vvv`) for more output detail.

### Run a single remote command

One-shot execution without keeping a shell.

```
ranger.py -u user -p 'Password1' -d CORP -t 198.51.100.10 --wmiexec -c "Net User"
```

Outcome: the command runs on the target through the chosen method and its output is returned — the README's example enumerates local accounts with `Net User`.

### Inject PowerShell Mimikatz in memory

Extract credentials without a payload file landing on disk.

```
ranger.py -u user -p 'Password1' -d CORP -t 198.51.100.10 --wmiexec --invoker
```

Outcome: a PowerShell payload invoking `Invoke-Mimikatz -DumpCreds` (the defaults; `-f` and `-a` override the function and its arguments) executes in memory on the target via the chosen method, with the command double-encoded to evade inspection.

### Dump local credential stores

Pull SAM and related secrets the Impacket way.

```
ranger.py -u user -p 'Password1' -d CORP -t 198.51.100.10 --secrets-dump
```

Outcome: runs the secrets-dump capability against the target and reports the extracted local credential material for further use.

### Map the domain's structure

Query membership and forest layout through an established method.

```
ranger.py -u user -p 'Password1' -d CORP -t 198.51.100.10 --wmiexec --get-domain-membership
```

Outcome: reports the domain membership context you are operating in; sibling attack flags cover `--get-forest`, `--get-forest-domains`, `--get-dc`, `--domain-group-members` and `--local-group-members`, and a follow-up `--domain` flag can target a different domain than the one you logged into.

*No corpus evidence — derived from upstream documentation (README at funkandwagnalls/ranger; every command form quoted from its usage section).*
