---
name: "smbexec"
categories:
  - blackarch-scanner
version: "59.a54fc14"
homepage: "https://github.com/brav0hax/smbexec"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "brav0hax/smbexec"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 263
github-pushed: "2017-09-06"
github-archived: false
github-issues: 9
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smbexec

> A menu-driven, Samba-based psexec-style attack suite for spinning up remote shells and harvesting credentials across many Windows hosts.

## What it's for

This is the original smbexec — conceived and first scripted by Brav0Hax
(Royce Davis) and PureHate around 2011 as a bash suite, then ported to
Ruby (v2.0, "Machiavellian") by Smilingraccoon and Zeknox. It exists
because Metasploit's PSExec module kept getting caught: smbexec instead
compiles a patched Samba client (`smbexeclient`) and `winexe`, generates
an obfuscated payload, and uploads-and-executes it on every host where
your credentials hold local admin, yielding a shell as the user or
SYSTEM. The console then layers on mass credential harvesting — domain
cached credentials (a standalone `cachedump.rb`), in-memory cleartext
passwords via the bundled Windows Credentials Editor, and `ntds.dit` +
SYSTEM extraction through a volume shadow copy — plus UAC state checks
and domain-admin hunting across the target list. It is an engagement-era
(2011–2013) Ruby/post-exploitation framework, not a single-purpose
binary: the bundled installer must compile its Samba/winexe helpers
before the first run.

Do not confuse it with impacket's `smbexec.py`, a one-shot remote-exec
script with the same name — that tool has its own note at
[[impacket-smbexec]].

## Typical use cases

### Drive the console against a target list

Launch the interactive suite once helpers are built.

```
sudo smbexec
```

Outcome: an msfconsole-style menu (the packaged launcher runs
`bundle exec ./smbexec.rb`) offering host-list generation, credential
checking, hash and cached-credential dumping, payload generation with
obfuscation, and upload-and-exec deployment; each action logs per-host
artifacts into the smbexec log directory, and a remote command shell can
be opened without deploying any payload at all.

*No corpus evidence — derived from upstream documentation (archived project README, v2.0).*
