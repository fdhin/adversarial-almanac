---
name: "linikatz"
categories:
  - blackarch-automation
version: "54.b06ec5f"
homepage: "https://github.com/portcullislabs/linikatz"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "portcullislabs/linikatz"
github-stars: 627
github-pushed: "2026-05-30"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# linikatz

> Mimikatz-style audit of Active Directory secrets on UNIX hosts.

## What it's for

`linikatz.sh` (Portcullis Labs / Cisco, from the research "Where 2 Worlds Collide: Bringing Mimikatz et al to UNIX") sweeps a Linux/UNIX machine for everything connecting it — and its users — to Active Directory, and steals what it finds: FreeIPA/SSSD/PBIS/Quest-VAS configurations and caches, Kerberos keytabs and ticket caches (machine and user), Samba secrets, SSH keys, and process memory of credential-handling processes. The point is that a compromised UNIX box joined to an AD forest yields domain credentials as readily as a compromised Windows one. Run it as root; unprivileged runs skip root-only checks with a warning. The repository also ships the defensive counterpart: auditd detection policies, John the Ripper rules, Metasploit post-exploitation modules and fuzzers.

## Typical use cases

### Run the full AD credential audit

Execute the sweep against the local host.

```
sudo ./linikatz.sh
```

Outcome: prints a staged, check-by-check report — FreeIPA / SSSD / VAS / PBIS configuration, machine and user Kerberos tickets, keytab contents, Samba secrets, SSH keys — listing each AD-related secret and cache found; root-only checks are skipped with a warning when run unprivileged, so compare privileged and unprivileged output to see what your exposure really is.

*No corpus evidence — derived from upstream documentation.*
