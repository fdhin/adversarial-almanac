---
name: "avml"
categories:
  - blackarch-misc
version: "v0.17.0.r3.g12c2a71"
homepage: "https://github.com/microsoft/avml"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-03-28"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "microsoft/avml"
github-stars: 1132
github-pushed: "2026-11-05"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 121
gh-activity-checked: "2026-11-09"
---

# avml

> Portable volatile-memory acquisition for Linux — a static binary, by Microsoft.

*No corpus evidence — derived from upstream documentation.*

## What it's for

AVML (Acquire Volatile Memory for Linux) snapshots physical memory on x86_64 Linux hosts without knowing the distribution or kernel in advance — no on-target compilation or fingerprinting. It is a self-contained Rust binary intended to be dropped on a host and run as root, reading from `/dev/crash`, `/proc/kcore` or `/dev/mem` (auto-detected in that preference order unless `--source` overrides) and writing a LiME-format image. Beyond plain local capture it can compress, upload the image to Azure Blob Storage or any HTTP PUT endpoint (including S3/GCS presigned URLs), or stream a snapshot straight off the box without touching local disk. It acquires; analysis (volatility & friends) is a separate step.

## Typical use cases

### Capture a memory image to a local file

Run on the target host as root.

```
avml acquire snapshot.lime
```

Outcome: writes a LiME-format physical-memory image to `snapshot.lime`, choosing the first working source (`/proc/kcore`, `/dev/crash`, `/dev/mem`); the resulting file is what Volatility 3's lime plugin and other Linux memory tooling consume.

### Capture a compressed image

Shrink the snapshot at acquisition time.

```
avml acquire --compress snapshot.lime.compressed
```

Outcome: same acquisition as above but the output file is compressed, cutting disk usage and transfer time on hosts where either is constrained; the file decompresses back to a raw/LiME image for analysis.

### Capture and upload off-host in one step

Push the image out as it completes, then remove the local copy.

```
avml acquire --url "${UPLOAD_URL}" --delete snapshot.lime
```

Outcome: after capture, the image is uploaded via HTTP PUT to the given (presigned) URL — any of Azure Blob, AWS S3 or GCP Storage presigned endpoints — and the local file is deleted on success, leaving no snapshot on the acquired host.

### Stream a snapshot without local disk

Send memory bytes directly to a collector over TCP.

```
nc -l 9000 > snapshot.lime
avml stream tcp collector.example.com:9000
```

Outcome: the first command listens on the collector host; on the target, `avml stream` picks the memory source once and writes the snapshot sequentially over the connection — nothing is written to target disk, which suits read-only roots and chain-of-custody requirements. The stream is plain TCP (no TLS, no resume): tunnel it if confidentiality matters.
