---
name: "tor-browser"
categories:
  - blackarch-defensive
version: "15.0.26"
homepage: "https://download.torproject.org/tor-browser-for-desktop/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: scripts
license: MPL-2.0
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tor-browser

> The Tor Project's privacy-hardened Firefox bundle, packaged with a launcher that maintains a per-user copy.

## What it's for

Tor Browser is the Tor Project's official browser: a Firefox fork tuned for anonymity that routes all traffic over the Tor network and defends against browser fingerprinting. Reach for it when you need censorship-resistant, surveillance-minimizing browsing, or a trusted client for checking that your own onion services resolve. The Arch-family package does not unpack the browser system-wide; it stores the official release tarball under `/opt/tor-browser` and ships a small `tor-browser` launcher that extracts and maintains a per-user copy in `~/.local/opt/tor-browser`. It is a GUI application — the command-line interface below drives the packaged launcher, not the browser internals.

## Typical use cases

### Launch Tor Browser

```
tor-browser
```

Outcome: on first run the launcher extracts the bundled tarball into `~/.local/opt/tor-browser/app` (progress and errors are logged to `~/.local/opt/tor-browser/LOG`) and opens the browser; subsequent runs start the browser directly.

### Show the launcher's management options

```
tor-browser --help
```

Outcome: prints usage — `-u`/`--update` (check the AUR for a newer package release and install it), `-r`/`--refresh` (re-extract the browser), `-e`/`--erase` (delete the user copy), and `--dir=<directory>` (keep the copy in an alternate location).

### Refresh the browser after a broken upgrade

```
tor-browser --refresh
```

Outcome: deletes and re-extracts the application files in `~/.local/opt/tor-browser/app` while preserving the browser profile directory, then launches Tor Browser with a clean binary set.

### Remove the per-user copy

```
tor-browser --erase
```

Outcome: removes `~/.local/opt/tor-browser` (application, profile, logs); the next plain `tor-browser` run re-extracts a pristine copy from `/opt`.

### Open a URL on startup

```
tor-browser https://example.com
```

Outcome: arguments the launcher does not recognize are passed through to the browser's `start-tor-browser` entry point, so Tor Browser opens that URL (or honors other Firefox-style arguments) on launch.

*No corpus evidence — derived from upstream documentation.*
