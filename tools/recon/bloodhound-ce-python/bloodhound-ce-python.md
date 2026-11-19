---
name: "bloodhound-ce-python"
categories:
  - blackarch-recon
version: "v1.0.1.r220.gf7c0144"
homepage: "https://github.com/dirkjanm/BloodHound.py"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-24"
meta-checked: "2026-11-09"
github-repo: "dirkjanm/BloodHound.py"
github-stars: 2465
github-pushed: "2026-10-24"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# bloodhound-ce-python

> Python data collector for BloodHound Community Edition (v5) — the CE-format sibling of `bloodhound-python`.

## What it's for

bloodhound-ce-python collects Active Directory data over LDAP for BloodHound Community Edition (CE, the v5+ web application that replaced the deprecated legacy 4.x [[bloodhound]] GUI). It is the `bloodhound-ce` branch of the same repository as the legacy ingestor [[bloodhound.py]] (originally Fox-IT, maintained by Dirk-jan Mollema), installed as a separate package (PyPI `bloodhound-ce`, console command `bloodhound-ce-python`) because the CE and legacy JSON formats are incompatible — the branch prints `INFO: BloodHound.py for BloodHound Community Edition` at startup while the legacy install prints a LEGACY banner. The CLI surface (credentials as password/hash/AES key/Kerberos ticket, `-d`, `-dc`, `-gc`, `-ns`, `-c` collection methods, `--zip`) is shared with bloodhound-python. CE also ships its own first-party collectors (SharpHound CE / AzureHound CE, downloadable from the CE UI), which cover Windows collection; this tool is the Linux-side path.

## Typical use cases

### Collect domain data for CE ingestion

The standard sweep with valid credentials:

```
bloodhound-ce-python -u user -p 'Password1' -d example.com -dc dc01.example.com --zip -c All
```

Outcome: prints `INFO: BloodHound.py for BloodHound Community Edition`, queries LDAP on the named domain controller and writes a CE-format zip ready for upload to the BloodHound CE web application.

### Collect with a Kerberos ticket

For ticket-only access, e.g. a machine account ccache:

```
export KRB5CCNAME=host.ccache
bloodhound-ce-python -u 'web01$' -k -no-pass -d example.com --zip -c All
```

Outcome: authentication from the Kerberos ccache without a password; machine-account collections show computer-side edges (local admin, delegation flags) that user tickets may not reach.

### Ingest the collection zip through the CE web UI

```
# CE web UI (default http://localhost:8080/ui/login):
# Administration -> Data Collection -> File Ingest -> Upload File(s)
# (or the left-menu Quick Upload button, then drag the zip in)
```

Outcome: the archive is queued and parsed into the graph; CE accepts .zip archives or bare JSON with no size limit. Re-collecting after compromising a new account and re-uploading is the normal loop — CE merges by object ID.

### Automate ingestion against the CE API

CE is API-first; everything the UI does is a REST call:

```
# create an API token in the CE UI (My Profile -> API Key Management), then
# POST the archive to the /api/v2/file-upload/ endpoints with
# 'Authorization: Bearer <JWT>' (or the HMAC API key/ID pair) headers
```

Outcome: scripted collect-and-ingest pipelines without touching the browser; request shapes are documented in the BloodHound API reference shipped with the instance (UI cog -> API Explorer).
