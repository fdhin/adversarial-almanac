---
name: "gvm"
categories:
  - vulnerability
version: "25.04.3"
homepage: "https://www.kali.org/tools/gvm/"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali, aur]
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gvm

> Kali metapackage that installs the Greenbone Vulnerability Management stack (formerly OpenVAS) and ships the scripts that set it up and operate it.

## What it's for

The `gvm` package pulls in the Greenbone Vulnerability Management components — gvmd (the
manager), openvas-scanner, notus-scanner, ospd-openvas and gsad (the web UI) — together with the
helper scripts that operate the whole stack (`gvm-setup`, `gvm-check-setup`, and the start/stop
wrappers). GVM itself is a modular vulnerability-management system: feed-driven network
scanning, result storage and reporting; the tool was previously named OpenVAS, and the scanner
component is covered in the openvas note. Installing this package is the one-step way to a
working Greenbone installation on Kali/Debian, but the package itself is only the glue — feed
synchronization, certificates and the database are brought up by the scripts, and first-time
setup is long. This is a service stack, not a point-and-shoot CLI scanner: plan for setup time
and feed updates.

## Typical use cases

### Initialize a fresh installation

Bootstrap the database, certificates and feeds after installing the package.

```
sudo gvm-setup
```

Outcome: one-shot bootstrap — creates/starts the PostgreSQL database, generates GVM's
certificate files and synchronizes the vulnerability feeds. Long-running on first use; when it
completes, the web UI and scanner services are ready to be started.

### Verify installation health

Check that every component of the stack is present and consistent.

```
sudo gvm-check-setup
```

Outcome: steps through each component (scanner presence and version, certificates, database,
feeds), printing `OK` or `ERROR` per check with a `FIX:` hint naming the command that repairs
it; repeat after fixing until it reports the installation complete.

*No corpus evidence — derived from upstream documentation (Kali tools page for gvm; the
`github-repo` field was removed — moovweb/gvm is the unrelated Go Version Manager).*
