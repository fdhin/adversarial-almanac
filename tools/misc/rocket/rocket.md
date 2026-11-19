---
name: "rocket"
categories:
  - misc
version: "2.0.0+parrot1"
homepage: "https://gitlab.com/parrotsec/project/rocket"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: distro-package
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rocket

> Parrot OS's PySide6 desktop app for launching containerized security tools through rootless Podman (or Docker).

## What it's for

Rocket is a cross-platform GUI launcher, packaged by Parrot OS, that keeps a searchable catalog of security tools — nmap, Bettercap, TShark, sqlmap, WPScan, Metasploit, NetExec, Katana, John the Ripper and more — and runs each one as a container through Podman or Docker, opening it in a dedicated terminal with per-tool runtime profiles. It prefers rootless Podman and never invokes it via sudo; Docker can only be used after explicit in-session approval that expires when the app closes. It also includes a Dockerfile manager that generates, edits, builds and runs images. There is no command-line workflow to document: the binary takes no flags and simply opens the window — the tool catalog, optional per-run arguments, image availability and container stop controls are all GUI operations. Upstream lives on GitLab at gitlab.com/parrotsec/project/rocket; on Parrot it installs as `/usr/bin/rocket` with a desktop entry.

## Typical use cases

### Open the launcher

Start the app and run a tool from the catalog.

```
rocket
```

Outcome: opens the PySide6 window with the tool catalog in the sidebar (icons show which images are already local); authorize rootless Podman if prompted, pick a tool, optionally enter arguments, and choose Run Container — the tool starts in its own terminal window, and Stop ends one or all matching containers.

*No corpus evidence — derived from upstream documentation.*
