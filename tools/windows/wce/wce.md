---
name: "wce"
categories:
  - blackarch-windows
version: "1.41beta"
homepage: "https://ampliasecurity.com/research/windows-credentials-editor"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-10-11"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "gavz/wce"
github-stars: 0
github-pushed: "2019-01-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wce

> Windows Credentials Editor — list, steal and swap NTLM credentials, Kerberos tickets and cleartext passwords in live logon sessions.

## What it's for

WCE (Windows Credentials Editor, Hernan Ochoa / Amplia Security) is a post-exploitation tool for Windows XP through Windows 8 that manipulates logon-session credentials: it lists the NTLM hashes held in memory by every logon session (interactive, service, RDP), dumps cleartext passwords stored by the Digest authentication package, exports and re-imports Kerberos tickets, and performs pass-the-hash by swapping the NTLM credentials of your current session or of a newly created one. It needs local administrator privileges on the host and works by reading LSASS structures directly ("safe mode") or via injection. WCE is unmaintained and heavily signatured by antivirus — modern equivalents include mimikatz ([[mimikatz]]) for dumping and impacket tooling such as [[impacket-secretsdump]] for offline credential work — but it remains a compact single-binary option for legacy targets.

GitHub: third-party mirror of the dead ampliasecurity.com distribution (v1.3beta release).

## Typical use cases

### Dump NTLM credentials from memory

Default invocation: harvest every logon session's LM/NT hashes.

```
wce.exe
```

Outcome: prints one line per credential in `user:domain:lmhash:nthash` form for all in-memory logon sessions; add `-o creds.txt` to also write them to a file.

### Dump cleartext logon passwords

Extract plaintext passwords kept by the Windows Digest authentication package.

```
wce.exe -w
```

Outcome: prints `DOMAIN\user:password` lines for sessions where Digest stored a cleartext secret — often blank on modern configs, valuable on legacy ones.

### Pass-the-hash in the current session

Replace your own session's NTLM credentials with stolen ones so subsequent Windows network authentication uses them.

```
wce.exe -s user:example.com:LMHASH:NTHASH
```

Outcome: reports changing the NTLM credentials of the current logon session; SMB/other NTLM auth from this session now authenticates as the target account without knowing its password.

### Launch a process under stolen credentials

Create a fresh logon session with arbitrary hashes and start a program inside it.

```
wce.exe -s user:example.com:LMHASH:NTHASH -c cmd.exe
```

Outcome: opens `cmd.exe` in a new logon session bound to the supplied NTLM credentials — commands in that window authenticate to remote systems as the target account.

*No corpus evidence — derived from upstream documentation (Amplia Security WCE page and official FAQ command examples).*
