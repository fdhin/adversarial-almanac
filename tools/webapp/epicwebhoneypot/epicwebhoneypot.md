---
name: "epicwebhoneypot"
categories:
  - blackarch-webapp
version: "2.0a"
homepage: "http://sourceforge.net/projects/epicwebhoneypot/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# epicwebhoneypot

> Tool which aims to lure attackers using various types of web vulnerability scanners by tricking them into believing that they have found a vulnerability on a host.

## What it's for

Epic Web Honeypot is not a program but a decoy artifact: a single large static `index.html` (about 2.4 MB) densely packed with fake attack payloads — XSS vectors, backdoor paths, upload forms, "virii", OS fingerprint strings borrowed from Nmap/Honeyd print lists, fake web server banners, sting-website URLs and more. The idea (2009-era, by "Malformation", GPLv2) is to drop it into a document root and let crawlers, bots and vulnerability scanners waste their time indexing and "finding" hundreds of vulnerabilities that do not exist; the README notes it fools the nikto scanner specifically. There is no CLI, no configuration and no runtime — deploying the HTML file is the entire usage. The package installs the artifact at `/usr/share/epicwebhoneypot/index.html`.

## Typical use cases

### Deploy the decoy page as your site root

Put the honeypot where scanners will hit it.

```
cp /usr/share/epicwebhoneypot/index.html /var/www/html/index.html
```

Outcome: any bot or scanner that crawls the host now sees a page littered with what look like exploitable endpoints, leaked files and XSS vectors — automated tools (nikto included) burn their run reporting bogus findings, and you observe who is scanning by what they request.

### Inspect what bait the artifact serves

Check the decoy content before deploying it.

```
grep -c -i "xss\|shell\|admin" /usr/share/epicwebhoneypot/index.html
```

Outcome: counts the decoy markers inside the artifact (thousands of matches), confirming the page's bait density; pair with `grep -o` on a keyword to sample the exact fake paths and payloads before exposing them on a host.

No corpus evidence — derived from the packaged upstream source (SourceForge `epicwebhoneypot-2.0a.rar` as referenced by the BlackArch PKGBUILD: README.txt, CHANGELOG.txt and the artifact itself; no CLI exists).
