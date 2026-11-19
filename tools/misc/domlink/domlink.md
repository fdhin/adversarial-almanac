---
name: "domlink"
categories:
  - blackarch-misc
version: "37.1cabd5d"
homepage: "https://github.com/vysecurity/DomLink"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "vysecurity/DomLink"
github-stars: 845
github-pushed: "2024-05-16"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domlink

> Pivots from a single domain to the registrant organisation, its e-mail addresses, and every other domain they have registered.

## What it's for

DomLink (by Vincent Yiu, @vysecurity) is scope-discovery tooling for bug bounty and red-team reconnaissance. Starting from one domain it reads the WHOIS record to extract the registrant organisation and contact e-mail, then uses the Whoxy reverse-WHOIS API to find every other domain registered to that organisation or e-mail, following newly discovered companies, e-mails and domains recursively. The linked domain set routinely includes forgotten infrastructure belonging to the same owner. It needs a Whoxy API key (a free-tier account works) supplied either via `-A` or a `domLink.cfg` file beside the script, and each recursion level consumes API credits — check the balance warnings it prints.

## Typical use cases

### Map everything linked to a domain

Write the API key into `domLink.cfg` next to the script, then run it against the seed domain.

```
printf '[API_KEYS]\nwhoxy = WHOXY_API_KEY\n' > domLink.cfg
python domLink.py example.com -o example.out.txt
```

Outcome: discovers the registrant organisation and e-mail for example.com plus all domains sharing them, and writes the linked companies, e-mails and domains to example.out.txt.

### Restrict the recursion to one pivot type

```
python domLink.py example.com -D -o domains-only.txt
```

Outcome: follows only the domain relationship recursively (`-D`); use `-E` to recurse on e-mails or `-C` on companies when a full three-way crawl would burn more API credits than the engagement needs.

*No corpus evidence — derived from upstream documentation.*
