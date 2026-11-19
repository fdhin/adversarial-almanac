---
name: "pbounce"
gentoo-category: "net-proxy"
version: "0.1"
homepage: "http://www.o0o.nu/projects/bouncers"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pbounce

> Pivoting bouncer from the o0o Security Team: multiplexes many TCP connections into an internal network over a single reverse connection through firewalls that allow only outbound traffic.

## What it's for

pbounce is a small C port-and-connection forwarder by Meder Kydyraliev (o0o Security Team, mid-2000s; a 2010 v0.1 release was packaged by Gentoo/Pentoo). Its distinguishing trick is connection multiplexing over a reverse connection: the pivot host calls out to the operator's machine — piercing firewalls that permit only outgoing connections — and multiple TCP streams toward internal services are encapsulated and carried through that single channel on demand, with a deliberately tiny binary footprint for Windows and Unix. It was designed as the "maintaining control of compromised hosts" component of an automated testing framework (alongside httpbee and YAWATT, presented by Fyodor Yarochkin), and reportedly offered remote command execution possibilities. The o0o.nu project site is gone and its README/tarball were never archived, so no verifiable command-line interface survives — treat this as an archived, research-era tool rather than a documented CLI. For equivalent modern workflows use SSH remote port forwards or purpose-built pivoting tools (chisel, ligolo-ng, rpivot).

*No corpus evidence — derived from upstream documentation (archived project page and conference transcript; no surviving CLI docs).*
