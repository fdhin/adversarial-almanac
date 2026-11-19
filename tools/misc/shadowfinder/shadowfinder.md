---
name: "shadowfinder"
categories:
  - blackarch-misc
version: "0.7.1.r0.g215485a"
homepage: "https://github.com/bellingcat/ShadowFinder"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "bellingcat/ShadowFinder"
github-stars: 624
github-pushed: "2026-09-03"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# shadowfinder

> Estimate where on Earth a shadow of a given length could fall, for photo and video geolocation.

## What it's for

Bellingcat's ShadowFinder narrows down where a photo or video was taken from the geometry of its shadows. Given an object's height and its shadow length (any consistent units — a person of known height, a landmark, pixels against a reference), plus date and time, it computes the implied sun altitude and renders every region of the world where such a shadow is possible at that moment as a bright band on a map. Combined with other clues in the image this cuts the candidate search area dramatically; used the other way round, `find_sun` starts directly from a measured sun-altitude angle. Results are a possibility band, not a point location, and are only as good as the measured ratio and the timestamp (check whether the time is UTC or local). Ships as a CLI, a Python API, and a Colab notebook; the timezone-grid computation is expensive and is cached to `timezone_grid.json` in the API, but regenerated on every CLI run.

## Typical use cases

### Map candidate regions from object height and shadow length

```
shadowfinder find 10 5 2024-02-29 13:59:59 --time_format=utc
```

Outcome: computes the sun altitude implied by the 10-to-5 height-to-shadow ratio at that instant and displays a world map with the matching band highlighted — anywhere on the band could produce that shadow. Use `--time_format=local` when the timestamp is local time at the scene.

### Map candidates from a measured sun altitude

Skip the height/length conversion when the sun angle is known directly (for example from other sun-position tooling).

```
shadowfinder find_sun 50 2024-02-29 13:59:59 --time_format=utc
```

Outcome: renders the same band plot from a sun altitude of 50 degrees above the horizon.

### Use the Python API for repeated lookups

Cache the expensive timezone grid across runs instead of regenerating it each time.

```python
from datetime import datetime
from shadowfinder import ShadowFinder

finder = ShadowFinder()
try:
    finder.load_timezone_grid()
except FileNotFoundError:
    finder.generate_timezone_grid()
    finder.save_timezone_grid()  # writes timezone_grid.json

finder.set_details(date_time=datetime(2024, 2, 29, 13, 59, 59),
                   object_height=10, shadow_length=5, time_format="utc")
finder.find_shadows()
fig = finder.plot_shadows()
```

Outcome: builds or reuses `timezone_grid.json`, runs the search, and returns the matplotlib figure for saving or further annotation.

*No corpus evidence — derived from upstream documentation.*
