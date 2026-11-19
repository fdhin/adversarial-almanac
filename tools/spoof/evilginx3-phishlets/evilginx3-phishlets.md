---
name: "evilginx3-phishlets"
categories:
  - spoof
version: "git (2025-05-27)"
homepage: "https://github.com/simplerhacking/evilginx3-phishlets"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "simplerhacking/evilginx3-phishlets"
github-stars: 758
github-pushed: "2025-05-27"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 14
---

# evilginx3-phishlets

> Simpler Hacking's curated collection of Evilginx 3.x phishlets — YAML files that each teach the proxy how to mirror a real site, rewrite its content and capture credentials and session tokens.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This is a data package, not a program: a repository of phishlet definitions maintained for penetration testers, updated as target sites change their login flows. Each file declares the minimum Evilginx version it needs (`min_ver`, typically 3.2.0+), the subdomains to proxy (`proxy_hosts`), content-rewrite rules (`sub_filters`), the cookies that mark an authenticated session (`auth_tokens`), which POST fields hold credentials (`creds`), the URLs that prove login success (`auth_urls`) and the form mapping for automated login testing (`login`). The collection exists because upstream Evilginx ships only a demo phishlet and its author refuses to provide real ones — community collections like this are the standard supply. Treat every file as untrusted configuration: review what it proxies and captures before deploying, and expect individual phishlets to rot as the mirrored sites change.

## Typical use cases

### Survey what sites the collection covers

One sentence of intent: see the available phishlets before building a campaign.

```
ls ~/evilginx3-phishlets/phishlets/
```

Outcome: lists one YAML file per supported target site; each filename (minus `.yaml`) is the phishlet name you will use in the Evilginx console.

### Vet a phishlet before trusting it

One sentence of intent: confirm the file parses and inspect what it captures.

```
python3 -c "import yaml,sys; d=yaml.safe_load(open(sys.argv[1])); print(d['name'], d['min_ver'], [t['keys'] for t in d.get('auth_tokens',[])])" ~/evilginx3-phishlets/phishlets/example.yaml
```

Outcome: parses the YAML and prints the phishlet's name, its minimum Evilginx version, and the cookie keys it hunts — a quick way to check compatibility with your Evilginx build and to see exactly which session tokens it will steal before you deploy anything.

### Install a phishlet into Evilginx

One sentence of intent: drop a chosen target into your running proxy's phishlets directory.

```
cp ~/evilginx3-phishlets/phishlets/example.yaml /opt/evilginx/phishlets/
```

Outcome: the phishlet becomes available to Evilginx; start (or restart) the server with that directory (`evilginx -p /opt/evilginx/phishlets`), then in the console run `phishlets hostname example login.phishing.example.com` and `phishlets enable example` to bring the mirrored site live under your domain.
