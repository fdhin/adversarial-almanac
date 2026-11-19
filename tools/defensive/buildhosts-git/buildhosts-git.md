---
name: "buildhosts-git"
categories:
  - defensive
version: "20170524.r12.99ed481"
homepage: "https://github.com/prurigro/buildhosts"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "prurigro/buildhosts"
github-stars: 6
github-pushed: "2024-11-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# buildhosts-git

> Builds /etc/hosts from user-configurable blocklist sources, with a one-command revert to the original file.

## What it's for

A small shell tool that merges downloaded hosts-file sources — ad-blocking and malware blocklists — with your own base hosts file to produce /etc/hosts. Sources are listed one URL per line in /etc/hosts.sources (lines starting with # are comments); the first run preserves the existing hosts file as /etc/hosts.core, and every later run rebuilds /etc/hosts from that core plus the downloaded lists. A revert command restores the pre-tool file. All locations are overridable through environment variables (HOSTS_DIR, HOSTS_SYSTEM, HOSTS_CORE, HOSTS_SOURCES, HOSTS_WHITELIST), and a bundled p2p-to-hosts script converts PeerGuardian-style p2p blocklists into hosts format for use as sources.

## Typical use cases

### Build /etc/hosts from configured blocklists

As root, generate the merged hosts file from the configured sources.

```
sudo buildhosts build
```

Outcome: on the first run /etc/hosts is moved to /etc/hosts.core and /etc/hosts.sources is created with default ad-blocking sources; each listed source is downloaded and a new /etc/hosts is written from the core file plus the merged lists. Re-running rebuilds from whatever /etc/hosts.sources now contains.

### Revert to the original hosts file

Undo the merge and restore the pre-buildhosts configuration.

```
sudo buildhosts revert
```

Outcome: /etc/hosts.core is moved back to /etc/hosts, removing every entry imported from sources; /etc/hosts.sources is left in place for manual editing or deletion.

### Build into an alternate location for testing

Preview what a build produces without touching /etc.

```
HOSTS_DIR="$HOME/lab-hosts" HOSTS_SYSTEM="$HOME/lab-hosts/hosts" buildhosts build
```

Outcome: the core, sources, whitelist and generated hosts files are all read and written under $HOME/lab-hosts instead of /etc, so the merged result can be inspected before promoting it system-wide.

*No corpus evidence — derived from upstream documentation.*
