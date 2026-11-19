---
name: "fwbuilder"
categories:
  - protect
version: "5.3.7-21"
homepage: "https://github.com/fwbuilder"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-01-05"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "fwbuilder/fwbuilder"
github-stars: 296
github-pushed: "2024-04-15"
github-archived: false
github-issues: 58
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fwbuilder

> Design firewall policies once in a GUI, then compile them into iptables, PF, ipfilter, ipfw, Cisco ASA/PIX or Cisco IOS ACL configurations.

## What it's for

Firewall Builder separates policy authoring from policy generation. You maintain firewalls, networks, hosts and services as objects in a central XML data file (.fwb) and edit rules with a drag-and-drop GUI; per-platform policy compilers — fwb_ipt, fwb_pf, fwb_ipf, fwb_ipfw, fwb_iosacl, fwb_pix — translate that model into native firewall configuration, scaling one policy across hundreds of firewalls. Because the GUI and the compilers are independent, the same rules can target a mixed estate (Linux iptables plus a Cisco ASA, say), and the compilers run headless for scripts and CI. The bundled fwbedit utility edits the object tree from shell scripts for batch processing. It does not inspect or block traffic itself; it produces and manages configuration.

## Typical use cases

### Open a policy file

Start the GUI with a specific data file loaded.

```
fwbuilder -f cluster.fwb
```

Outcome: the GUI launches with that firewall policy file open, ready for editing.

### Print a firewall's rules without the GUI

Render a rule report for review or documentation.

```
fwbuilder -f cluster.fwb -P firewall-gw -o rules.pdf
```

Outcome: prints the rules and objects of the firewall object `firewall-gw` to the given output file (or print.pdf in the current directory when -o is omitted) and exits without entering interactive mode.

### Compile a policy headless

Generate the target firewall script from the data file on a server or in CI.

```
fwb_ipt -f cluster.fwb firewall-gw
```

Outcome: reads the firewall object `firewall-gw` from cluster.fwb and writes the generated iptables script — named after the firewall object plus a `.fw` extension unless -o overrides — without launching the GUI.

*No corpus evidence — derived from upstream documentation.*
