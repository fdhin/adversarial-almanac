---
name: "commentor"
categories:
  - blackarch-webapp
version: "20.4582674"
homepage: "https://github.com/D3vil0p3r/comMENTOR"
packaged-by: [blackarch]
blackarch-builddate: "2023-09-22"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/comMENTOR"
github-stars: 7
github-pushed: "2023-08-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# commentor

> Pull every developer comment out of a web resource — HTML, JS, CSS or script files.

## What it's for

comMENTOR fetches a URL and extracts all comments in the syntax you name: `xml` for `<!-- -->` blocks (HTML/XML/ASP/ASPX pages), `cstyle` for `//` and `/* */` (PHP, JavaScript, CSS), `hashtag` for `#` comments (shell scripts and config files served over HTTP). Left-in comments are a classic recon source — internal hostnames, TODOs, disabled debug endpoints, credential fragments — and this tool collects them in one pass instead of grepping by hand. It is a bash pipeline (curl + tidy + xmlstarlet) with a single-purpose interface; it does not crawl, it processes exactly the URL you give it.

## Typical use cases

### Extract HTML comments from a page

Pull the `<!-- -->` blocks out of a web page.

```
commentor -u https://target.example.com/ -c xml
```

Outcome: fetches the page and prints every XML-style comment block it contains; scan the output for internal paths, notes and disabled features. Add `-o comments.txt` to write the results to a file instead.

### Extract comments from JavaScript or CSS

Target `//` and `/* */` style comments.

```
commentor -u https://target.example.com/static/app.js -c cstyle
```

Outcome: fetches the script/stylesheet and prints its C-style comments — often TODOs, feature flags or API hints left from development.

### Extract comments from a hosted shell script

Handle `#` comments in files like installers served over HTTP.

```
commentor -u https://target.example.com/install.sh -c hashtag
```

Outcome: prints every hashtag comment from the file, exposing configuration assumptions and internal endpoints referenced by the script's author.

No corpus evidence — derived from upstream documentation.
