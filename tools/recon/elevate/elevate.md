---
name: "elevate"
categories:
  - blackarch-recon
version: "27.1272d51"
homepage: "https://github.com/Healdb/Elevate"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unkown
meta-checked: "2026-11-09"
github-repo: "Healdb/Elevate"
github-stars: 74
github-pushed: "2023-05-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# elevate

> Horizontal domain discovery: find other domains registered to the same organization from a domain, an email address, or a partial company name.

## What it's for

Elevate (Healdb) is a horizontal domain discovery tool — despite the name it has nothing to do with privilege escalation. It takes a partial organization name, an email address, or a domain name and enumerates other domain names owned by the same company by querying reverse-WHOIS and ASN registry data through the Whoxy and WhoisXML APIs. Reach for it during OSINT on a target organization to surface related infrastructure (acquisitions, staging/marketing domains) that pure subdomain enumeration misses. It needs free-tier API accounts from whoxy.com and whoisxmlapi.com, with both keys placed in `config.txt` before running. It does not resolve, port-scan or otherwise touch the domains it finds — it only produces the ownership list.

## Typical use cases

### Discover sibling domains from a domain and email

Seed the search with both a domain and a known contact address.

```
python elevate.py -o org_domains.txt -d example.com -e user@example.com
```

Outcome: writes every domain registered to the same organization to `org_domains.txt`, combining what reverse-WHOIS finds from the domain registrant and from the email address.

### Discover sibling domains from a domain only

Use the registrant of one known domain as the pivot.

```
python elevate.py -o org_domains.txt -d example.com
```

Outcome: resolves the domain's WHOIS registrant organization and lists all other domains registered under it in `org_domains.txt`.

### Pivot from a single email address

Start with nothing but an organizational email.

```
python elevate.py -o org_domains.txt -e user@example.com
```

Outcome: reverse-WHOIS lookup on the email address returns all domains associated with it, written to `org_domains.txt` — useful when only a contact address is known.

### Search by partial organization name

When no domain or email is known, match the company name against the ASN registry.

```
python elevate.py -o org_domains.txt -n "Example Org"
```

Outcome: prompts interactively to pick the ASN registrations whose names match the search term, then enumerates domains registered to the selected organization into `org_domains.txt`.

No corpus evidence — derived from upstream documentation.
