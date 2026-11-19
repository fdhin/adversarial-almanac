---
name: "ghost-py"
categories:
  - blackarch-webapp
version: "2.0.0"
homepage: "http://jeanphix.github.com/Ghost.py/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ghost-py

> Python library that drives a real WebKit browser (via PySide/PyQt) for scripted page fetching.

## What it's for

Ghost.py is an importable Python module — not a standalone CLI — that wraps a Qt WebKit browser, letting scripts open pages, wait for rendering, and read the page content exactly as a browser produced it, which plain HTTP clients cannot. It belongs to the pre-headless-Chrome era and depends on PySide or PyQt bindings; upstream has been inactive for years, so for new automation a Playwright/Selenium-style stack is the modern replacement. Everything happens through the `ghost` module's Python API; there is no command to run.

## Typical use cases

### Fetch a page with full JavaScript rendering

Open a URL in a scripted WebKit session and inspect the rendered result.

```python
from ghost import Ghost

ghost = Ghost()
with ghost.start() as session:
    page, extra_resources = session.open("http://example.com")
    print(page.http_status, "example" in page.content)
```

Outcome: loads the URL in WebKit, waits for the page, and returns the page object (HTTP status, post-JavaScript content) plus fetched sub-resources, enabling assertions on dynamically rendered content.

No corpus evidence — derived from upstream documentation.
