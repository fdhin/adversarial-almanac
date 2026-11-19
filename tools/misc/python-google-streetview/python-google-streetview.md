---
name: "python-google-streetview"
categories:
  - blackarch-misc
version: "1.2.9"
homepage: "https://pypi.org/project/google-streetview/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
aliases: ["python2-google-streetview"]
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# python-google-streetview

> Command-line tool and Python module for the Google Street View Image API.

## What it's for

Wraps the Google Street View Image API: given coordinates, and optionally a heading, field of view, and pitch, it retrieves the available street-level panorama imagery — either interactively from the console via the `google_streetview` command or programmatically through the `google_streetview.api` module. Typical security use is OSINT reconnaissance of a physical location: establishing what a camera standing at those coordinates would see, or bulk-collecting imagery around a site of interest. It requires a Google API developer key, and Google's 2019 Street View pricing changes mean a billing plan may be required — the tool only retrieves API results and does not analyze the imagery itself.

## Typical use cases

### Store the Google API developer key

Register the key once so subsequent invocations can query the API.

```
google_streetview -s key="your_dev_key"
```

Outcome: the developer key is set for the tool to use in later calls; queries without a key fail with an API error.

### Download the imagery for a location

Fetch and save the street-level images available at a coordinate pair.

```
google_streetview --location="46.414382,10.013988" --save_downloads=downloads
```

Outcome: the panorama images for the location are written as files into the `downloads/` directory.

### Capture a full 360-degree view

Rotate the virtual camera across all four headings at a 90-degree field of view.

```
google_streetview --location="46.414382,10.013988" --fov=90 --heading=0;90;180;270
```

Outcome: one image per heading (north, east, south, west) is retrieved; together the four 90-degree shots cover the full horizon at that spot.

### Query the API from Python

Use the library directly inside a script for batch collection.

```
python3 - <<'EOF'
import google_streetview.api
params = [{
    'size': '600x300',
    'location': '46.414382,10.013988',
    'key': 'your_dev_key'
}]
results = google_streetview.api.results(params)
results.download_links('downloads')
EOF
```

Outcome: an API result object with metadata and image links for the location is created, and the linked images are downloaded into `downloads/`.

*No corpus evidence — derived from upstream documentation.*
