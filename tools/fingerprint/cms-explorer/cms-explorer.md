---
name: "cms-explorer"
categories:
  - blackarch-fingerprint
version: "15.23b58cd"
homepage: "https://github.com/FlorianHeigl/cms-explorer"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "FlorianHeigl/cms-explorer"
github-stars: 25
github-pushed: "2015-03-30"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cms-explorer

> CMS extension enumerator — brute-forces which plugins, components and themes a WordPress, Drupal, Joomla! or Mambo site has installed by requesting each candidate name and reading the response codes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Enumerating the installed extensions of a CMS-driven site, typically to find abandoned or vulnerable plugins and themes worth attacking. cms-explorer (Perl, by Chris Sullo of Nikto fame, 2010) requests each name from its bundled wordlists — the WordPress and Drupal lists are pulled directly from those projects' repositories, Joomla!/Mambo lists are hand-curated — and treats non-404 responses as installed. Discovered items can be routed through a "bootstrap proxy" (e.g. Burp or ZAP), and with `-explore` each found item's file listing is fetched from the vendor's repository and requested through that proxy for closer manual inspection (WordPress and Drupal only). The bundled lists can be refreshed with `-update`; the `-osvdb` vulnerability-lookup option is historical, since OSVDB no longer exists.

## Typical use cases

### Enumerate plugins and themes

Discover the installed extensions of a known CMS.

```
cms-explorer -url http://target.example.com/ -type wordpress
```

Outcome: walks the bundled WordPress plugin and theme lists (both on by default), requesting each candidate name and printing every plugin/theme whose response indicates it is installed; omit `-type` to let it auto-guess the CMS.

### Enumerate with custom wordlists

Target specific extensions instead of the full bundled lists.

```
cms-explorer -url http://target.example.com/ -type wordpress -plugins -pluginfile my_plugins.txt
```

Outcome: restricts checking to plugins using your own list instead of the bundled one (`-themefile` works the same for themes) — useful for focused checks of named extensions.

### Route discoveries through an intercepting proxy

Send found items and their files to Burp/ZAP for manual testing.

```
cms-explorer -url http://target.example.com/ -v 3 -bsproxy http://127.0.0.1:8080/ -explore
```

Outcome: auto-guesses the CMS type, sends any found plugins/themes plus each item's repository file listing (`-explore`, WordPress/Drupal) through the bootstrap proxy at 127.0.0.1:8080 where you can inspect and probe them; `-v 3` enables maximum verbosity.
