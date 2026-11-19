---
name: "air"
categories:
  - blackarch-forensic
version: "2.0.0"
homepage: "https://sourceforge.net/projects/air-imager/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# air

> Perl/Tk GUI front-end to dd and dc3dd for creating and verifying forensic disk images.

## What it's for

AIR (Automated Image & Restore) is a graphical front-end for `dd` and
`dc3dd` that makes acquiring forensic images of disks and partitions harder
to get wrong than a hand-typed command line. From the GUI you choose a source
device or partition, an image destination (file, split segments, or a remote
listener), and it builds and runs the underlying `dd`/`dc3dd` command for
you. It verifies images against the source with MD5 or SHA1–SHA512, supports
gzip/bzip2 compression, splitting images into user-defined segments, wiping
(zeroing) drives, imaging over a TCP/IP network via netcat/cryptcat, SCSI
tape drives, and writes a detailed session log of what was run. It is
Linux-only, written in Perl/Tk, and last released in 2010 — it does imaging
only, not analysis.

## Typical use cases

### Launch the imaging GUI

Start AIR with root privileges so it can open raw block devices.

```
sudo air
```

Outcome: opens the AIR window where you pick the source device, image file
destination, hash algorithm, compression, and split size; the equivalent
`dd`/`dc3dd` command it builds is written to the session log together with
hashes on completion.

### Image over the network

On the acquisition machine point the image destination at a remote netcat
listener instead of a local file (requires `nc` or `cryptcat` installed).

```
sudo air
```

Outcome: with the network option selected, AIR streams the image to the
listener you configure (e.g. `nc -l -p 9000 > image.dd` on the receiving
host) and logs the transfer; the receiver ends up with the same image and
hashes recorded on the sending side.

*No corpus evidence — derived from upstream documentation.*
