---
name: "azurehound"
categories:
  - blackarch-misc
version: "3.1.3.r0.gb02d457"
homepage: "https://github.com/SpecterOps/AzureHound"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-24"
build-system: go
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "SpecterOps/AzureHound"
github-stars: 983
github-pushed: "2026-10-16"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 103
gh-activity-checked: "2026-11-09"
---

# azurehound

> The BloodHound data collector for Microsoft Azure.

*No corpus evidence — derived from upstream documentation.*

## What it's for

AzureHound enumerates a Microsoft Azure tenant — users, groups, service principals, roles, management groups and the relationships between them — and emits the JSON that BloodHound Community Edition and BloodHound Enterprise ingest to graph attack paths in Azure (and Azure→Active Directory hybrid paths). It is the Azure counterpart to SharpHound, which covers on-prem AD: it collects, it does not exploit. Authentication is flexible: username/password against a tenant, an already-acquired JWT or refresh token, or a token reused from an authenticated Azure CLI session.

## Typical use cases

### Dump all tenant data to stdout

Authenticate with credentials and collect everything.

```
azurehound list -u "$USERNAME" -p "$PASSWORD" -t "$TENANT"
```

Outcome: authenticates to the tenant and streams every collected Azure object type to stdout as JSON — suitable for piping into a file or a BloodHound ingest endpoint. Expect large output on real tenants.

### Collect to a file for offline ingest

Write the collection to disk instead of stdout.

```
azurehound list -u "$USERNAME" -p "$PASSWORD" -t "$TENANT" -o mytenant.json
```

Outcome: same enumeration, but results land in `mytenant.json`, which you later upload to BloodHound (CE's GUI or API ingest) for graphing — the usual workflow when the collector runs on a different host than the BloodHound server.

### Reuse an Azure CLI session

Skip password auth by passing a token you already hold.

```
JWT=$(az account get-access-token --resource https://graph.microsoft.com | jq -r .accessToken)
azurehound list --jwt "$JWT"
```

Outcome: uses the Microsoft Graph access token from an authenticated `az` login (or any JWT you hold) instead of credentials — handy with service principals, managed identities, or when MFA makes interactive password auth impractical. A refresh token works the same way via `-r`.

### Set up the BloodHound Enterprise collection service

Configure once, then run the collector as a service.

```
azurehound configure
azurehound start
```

Outcome: `configure` walks an interactive prompt to store tenant and BloodHound Enterprise connection settings; `start` then runs continuous data collection for BHE — the always-on alternative to one-shot `list` dumps.
