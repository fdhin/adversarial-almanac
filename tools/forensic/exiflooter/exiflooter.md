---
name: "exiflooter"
categories:
  - blackarch-forensic
version: "40.b8cc568"
homepage: "https://github.com/aydinnyunus/exifLooter"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-21"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "aydinnyunus/exifLooter"
github-stars: 501
github-pushed: "2026-01-16"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# exiflooter

> Find EXIF geolocation in images — files, directories, or piped URL lists — and map it via OpenStreetMap.

## What it's for

Exif Looter (Yunus Aydın) hunts for GPS coordinates left in image
metadata — the classic OSINT and bug-bounty finding of uploaded photos
that still carry where they were taken. It drives exiftool (which must
be installed and on PATH) and reports the EXIF geolocation of whatever
you point it at: a single image, a directory of images, or a list of
URLs piped in from harvesting tools such as waybackurls. Found
coordinates can be turned into OpenStreetMap URLs for one-click
mapping, and it can also strip metadata from images. It is a
geolocation-focused scanner, not a general metadata browser — for full
EXIF inspection use exiftool or [[exifprobe]] directly.

## Typical use cases

### Check a single image

Quick look at one suspect photo.

```
exifLooter --image image.jpeg
```

Outcome: the image's EXIF metadata is parsed and any GPS coordinates
are reported; an image without geolocation comes back clean.

### Sweep a directory of images

Process a whole collection at once.

```
exifLooter -d images/
```

Outcome: every image under `images/` is checked and those containing
geolocation are flagged — first pass over an evidence or download
directory.

### Triage harvested URLs

Feed it image URLs collected from a target.

```
cat urls | exifLooter --pipe
```

Outcome: each URL is fetched and inspected for GPS EXIF — designed for
pipelines like `waybackurls target.example.com | grep "jpeg\|png\|jpg"
| exifLooter --pipe`, where site-uploaded images are checked for
leaked locations.

### Map findings on OpenStreetMap

Turn coordinates into a browsable location.

```
exifLooter --open-street-map --image image.jpeg
```

Outcome: for each image that carries GPS data an OpenStreetMap URL is
produced, ready to paste into a browser; works with `--directory` too.

### Strip metadata before publishing

Remove the leak instead of just reporting it.

```
exifLooter --remove --directory images/
```

Outcome: EXIF metadata, including geolocation, is removed from the
images in the directory — a remediation step, so run it on copies if
the originals are evidence.

*No corpus evidence — derived from upstream documentation.*
