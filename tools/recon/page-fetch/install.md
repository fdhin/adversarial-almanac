---
tool: "page-fetch"
build-system: "go"
upstream: "https://github.com/detectify/page-fetch"
recipe: "upstream"
version: "git (2026-07-03)"
license: "see repository"
---

# Installing page-fetch

> Fetch web pages using headless Chrome, storing all fetched resources including JavaScript files. Run arbitrary JavaScript on many web pages and see the returned values

Source: upstream [`detectify/page-fetch`](https://github.com/detectify/page-fetch); discovered via the awesome-oneliner-bugbounty review.

## From source

```bash
git clone https://github.com/detectify/page-fetch.git
cd page-fetch
go build ./...
```

Backlinks: [[recon]] · [[page-fetch]]
