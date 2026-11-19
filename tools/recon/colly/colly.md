---
name: "colly"
categories:
  - recon
version: "2.1.0-0parrot1"
homepage: "https://github.com/gocolly/colly"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "gocolly/colly"
github-stars: 25578
github-pushed: "2026-10-16"
github-archived: false
github-issues: 195
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 45
gh-activity-checked: "2026-11-09"
---

# colly

> Lightning-fast, elegant web scraping and crawling framework for Go.

## What it's for

Colly is a Go library (module `github.com/gocolly/colly/v2`), not a stand-alone
command: you write a small Go program against its callback API and it handles
HTTP, cookie/session state, politeness limits, caching and robots.txt for you.
It is the de-facto standard building block for custom crawlers — targeted
content collection during recon, archiving, data mining — and comfortably
exceeds 1,000 requests/second on a single core. It does not ship scrapers
itself; the intelligence (what to visit, what to extract) lives in your code.
Reach for it when generic CLI scrapers are too inflexible for the target site.

## Typical use cases

### Crawl a site and collect every link

Write a collector that follows every anchor while staying inside the target's
domains.

```go
package main

import (
	"fmt"

	"github.com/gocolly/colly/v2"
)

func main() {
	c := colly.NewCollector(
		colly.AllowedDomains("example.com", "www.example.com"),
	)
	c.OnHTML("a[href]", func(e *colly.HTMLElement) {
		link := e.Attr("href")
		fmt.Printf("Link found: %q -> %s\n", e.Text, link)
		c.Visit(e.Request.AbsoluteURL(link))
	})
	c.OnRequest(func(r *colly.Request) {
		fmt.Println("Visiting", r.URL.String())
	})
	c.Visit("https://example.com/")
}
```

Outcome: `go run .` inside a module that imports colly v2 prints one "Visiting"
line per request and every link found on each page; the AllowedDomains filter
keeps the crawl confined to example.com, so the run terminates once all
in-scope pages are fetched.

### Scrape politely with bounded parallelism

Cap concurrent requests and let the collector queue the rest instead of
hammering the target.

```go
package main

import (
	"fmt"

	"github.com/gocolly/colly/v2"
)

func main() {
	c := colly.NewCollector(
		colly.Async(),
	)
	c.Limit(&colly.LimitRule{
		DomainGlob:  "example.com",
		Parallelism: 2,
	})
	for i := 0; i < 5; i++ {
		c.Visit(fmt.Sprintf("https://example.com/page/%d", i))
	}
	c.Wait()
}
```

Outcome: all five pages are fetched asynchronously but never more than two
requests to example.com are in flight at once; `c.Wait()` blocks until the
queue drains, after which the program exits. Swap in a `Delay` field in the
LimitRule to also force spacing between requests to the same domain.

No corpus evidence — derived from upstream documentation (README and the
`_examples/basic` and `_examples/rate_limit` sources).
