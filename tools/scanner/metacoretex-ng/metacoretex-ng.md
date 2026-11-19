---
name: "metacoretex-ng"
gentoo-category: "net-analyzer"
version: "9999"
homepage: "http://metacoretex-ng.sourceforge.net"
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

# metacoretex-ng

> Community fork of the MetaCoretex database vulnerability scanner.

## What it's for

MetaCoretex-NG is an entirely Java framework for vulnerability-scanning databases, forked in 2004 after the original MetaCoretex project was abandoned (see [[metacoretex]] for the parent tool). It kept the probe architecture and extended it: any JDBC type-4 driver can be targeted, probes share a per-target knowledge base capable of holding live objects (a probe's opened database connection is reused by later probes), each probe declares its own user options without UI changes, scanning is threaded, and scan configurations and reports are stored as XML. It was designed to run anywhere Java runs, compiling user-written probes on the fly via the bundled javac classes. The SourceForge pages that documented its usage are empty today and no source repository survives, so no runnable examples can be given — treat it as the archived fork whose probe-plus-knowledge-base design carried forward into modern database scanners.

*No corpus evidence — description only: the upstream documentation pages no longer provide a usable CLI reference.*
