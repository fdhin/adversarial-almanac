---
name: "bingoo"
categories:
  - blackarch-scanner
version: "3.698132f"
homepage: "https://github.com/Hood3dRob1n/BinGoo"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Hood3dRob1n/BinGoo"
github-stars: 240
github-pushed: "2018-10-06"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bingoo

> BinGoo — an all-in-one dorking toolkit in pure bash: mass Bing and Google dork scans from dork lists, geo dorking by country or TLD, shared-hosting checks, then analysis of the harvested links for common vulnerability signs, with an admin-page finder and sqlmap/fimap wrappers.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Running the dork workflow without leaving the terminal. BinGoo scrapes the Bing and Google search pages directly (via lynx/curl) for links matching your terms — a single dork typed in or a file of dorks, one per line, with ready-made lists shipped in its `dorks/` directory. Bing is the dependable engine here: Google rate-blocks scrapers quickly, so the practical rhythm is Bing-first with Google interleaved. Beyond collection it layers the follow-up moves: Geo dorking filtered by site type or country code, a shared-hosting checker that hunts other sites on the same server, an analysis menu that replays the harvested links through checks for common vulnerability signs and sorts hits into per-class files, an admin-page finder driven from a list plus response codes, and wrappers that hand targets to sqlmap and fimap for confirmation. It is menu-driven throughout — everything happens through the interactive menus, with no non-interactive flag surface to script.

## Typical use cases

### Run a dork-and-analyze session

```
bingoo
```

Outcome: opens the main menu; pick an engine (Bing or Google), then supply a single dork or a dork file, and scraped result links land in `b-links.txt` (Bing) or `g-links.txt` (Google) in the working directory. From there the Analyze menu replays those link files through the vulnerability-sign checks and writes confirmed hits sorted into per-class files, the Geo/shared-hosting menus re-run collection against a country code or the other sites on a server, and the LFI/SQL sub-menus validate candidates via the built-in testers or the sqlmap/fimap wrappers. If a Google pass returns nothing, the scraper is temporarily IP-blocked — switch to Bing until it clears.
