---
name: "guymager"
categories:
  - blackarch-forensic
version: "0.8.13"
homepage: "http://guymager.sourceforge.net/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2022-03-05"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "cyberknightX/Guymager"
github-stars: 13
github-pushed: "2022-02-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# guymager

> Qt-based GUI forensic imager that acquires media as dd, EWF (E01) or AFF images at multi-threaded speed, with cloning support.

## What it's for

Guymager (Guy Voncken) is a Linux forensic imager with a localized Qt user interface, built on libewf and libguytools. Its distinguishing strength is speed: the acquisition pipeline and data compression are multi-threaded, so it saturates multi-processor machines during imaging. It writes flat (dd) images, EWF (E01) images including compression and split files, and AFF, and it can clone media directly. Acquisition is driven entirely from the GUI — right-click a device, fill in case info, and image with verification — so it is a workstation tool rather than a scripted pipeline component. Distro packages track the 0.8.x series; the project homepage on SourceForge is the authoritative source.

## Typical use cases

### Acquire a device image

Image attached media with verification.

```
sudo guymager
```

Outcome: the Qt window lists block devices with model, size and status; right-click the device and choose image acquisition, then set format (dd/E01/AFF), destination, compression and hashing options — acquisition runs multi-threaded with live progress and is verified on completion.

### Clone one device to another

Duplicate media without an intermediate image file.

```
sudo guymager
```

Outcome: choosing the clone action on the source device writes directly to a target device of matching size — useful for working copies of original evidence media while the original stays sealed.

*No corpus evidence — derived from upstream documentation.*
