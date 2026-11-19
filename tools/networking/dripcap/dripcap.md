---
name: "dripcap"
categories:
  - blackarch-networking
version: "0.6.15"
homepage: "https://github.com/dripcap/dripcap"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dripcap/dripcap"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# dripcap

> Discontinued cross-platform GUI packet analyzer with JavaScript protocol-decoder plugins.

## What it's for

Dripcap — the "Caffeinated Packet Analyzer", developed under the h2so5 GitHub account per the project's CI badges — was an Electron-based desktop packet sniffer for macOS, Linux and Windows, distributed as ready-made installers (dmg, deb, zip). Its distinguishing feature was a plugin system in which protocol dissectors and quick filters were written as plain JavaScript modules, so the GUI could be taught new decoders without recompiling anything. Development ended and the project moved to a successor repository (genet-app/genet), which has since disappeared too; the original GitHub repository is deleted, and with it the releases, wiki and any usable documentation. The BlackArch package simply repackaged the vendor's last release .deb, so the tool remains identifiable but effectively unmaintained. No command-line interface is documented in what survives (the archived README contained only the project name, tagline and download links), so this note is description-only.

*No corpus evidence — derived from archived upstream documentation (Wayback snapshot of the former github.com/dripcap/dripcap repository).*
